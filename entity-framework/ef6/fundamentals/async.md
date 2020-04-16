---
title: Query asincrona e salvataggio - EF6Async query and save - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434240"
---
# <a name="async-query-and-save"></a>Query asincrona e salvataggio
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

EF6 ha introdotto il supporto per la query asincrona e il salvataggio utilizzando le [parole chiave async e await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) che sono state introdotte in .NET 4.5. Sebbene non tutte le applicazioni possano trarre vantaggio dall'asincronia, può essere utilizzata per migliorare la velocità di risposta dei client e la scalabilità del server durante la gestione di attività a esecuzione prolungata, di rete o associate a I/O.

## <a name="when-to-really-use-async"></a>Quando usare veramente asyncWhen to really use async

Lo scopo di questa procedura dettagliata è introdurre i concetti asincroni in modo da semplificare l'osservazione della differenza tra l'esecuzione asincrona e sincrona del programma. Questa procedura dettagliata non ha lo scopo di illustrare uno degli scenari chiave in cui la programmazione asincrona offre vantaggi.

La programmazione asincrona si concentra principalmente sulla liberazione del thread gestito corrente (thread che esegue codice .NET) per eseguire altre operazioni durante l'attesa di un'operazione che non richiede alcun tempo di calcolo da un thread gestito. Ad esempio, mentre il motore di database sta elaborando una query non c'è nulla da fare dal codice .NET.

Nelle applicazioni client (WinForms, WPF e così via) il thread corrente può essere utilizzato per mantenere l'interfaccia utente reattiva durante l'esecuzione dell'operazione asincrona. Nelle applicazioni server (ASP.NET e così via) il thread può essere utilizzato per elaborare altre richieste in ingresso, in grado di ridurre l'utilizzo della memoria e/o aumentare la velocità effettiva del server.

Nella maggior parte delle applicazioni che utilizzano async non avrà vantaggi evidenti e anche potrebbe essere dannoso. Usare test, profilatura e buon senso per misurare l'impatto di async nello scenario specifico prima di eseguirne il commit.

Ecco alcune altre risorse da acquisire informazioni su async:Here are some more resources to learn about async:

-   [Panoramica di Brandon Bray su async/await in .NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   [Pagine di programmazione asincrona](https://msdn.microsoft.com/library/hh191443.aspx) in MSDN Library
-   [Come compilare ASP.NET applicazioni Web utilizzando Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (include una dimostrazione di aumento della velocità effettiva del server)How to Build ASP.NET Web Applications Using Async (includes a demo of a increased server throughput)

## <a name="create-the-model"></a>Creare il modello

Utilizzeremo il flusso di [lavoro Code First](~/ef6/modeling/code-first/workflows/new-database.md) per creare il modello e generare il database, tuttavia la funzionalità asincrona funzionerà con tutti i modelli di EF, inclusi quelli creati con la finestra di progettazione di EF.

-   Creare un'applicazione console e chiamarla **AsyncDemoCreate** a Console Application and call it AsyncDemo
-   Aggiungere il pacchetto NuGet di EntityFrameworkAdd the EntityFramework NuGet package
    -   In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AsyncDemoIn** Solution Explorer, right-click on the AsyncDemo project
    -   Selezionare **Gestisci pacchetti NuGet...**
    -   Nella finestra di dialogo Gestisci pacchetti NuGet, selezionare la scheda **Online** e scegliere il pacchetto **EntityFramework**
    -   Fare clic su **Installa**
-   Aggiungere una classe **Model.cs** con l'implementazione seguenteAdd a Model.cs class with the following implementation

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a>Creare un programma sincrono

Ora che abbiamo un modello di EF, è possibile scrivere codice che lo usa per eseguire l'accesso ai dati.

-   Sostituire il contenuto di **Program.cs** con il codice seguente

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

Questo codice chiama il metodo **PerformDatabaseOperations** che salva un nuovo **oggetto Blog** nel database, quindi recupera tutti i **Blog** dal database e li stampa **nella console**. Dopo questo, il programma scrive una citazione del giorno alla **Console**.

Poiché il codice è sincrono, è possibile osservare il seguente flusso di esecuzione quando si esegue il programma:Since the code is synchronous, we can observe the following execution flow when we run the program:

1.  **SaveChanges** inizia a eseguire il push del nuovo **blog** nel database
2.  **SaveChanges** viene completato
3.  La query per tutti i **blog** viene inviata al database
4.  La query restituisce e i risultati vengono scritti **nella console**
5.  Citazione del giorno è scritto su **Console**

![Uscita di sincronizzazione](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Renderlo asincrono

Ora che abbiamo il nostro programma in funzione, possiamo iniziare a fare uso del nuovo async e attendere parole chiave. Sono state apportate le seguenti modifiche aProgram.cs

1.  Riga 2: L'istruzione using per lo spazio dei nomi **System.Data.Entity** consente di accedere ai metodi di estensione asincrona di Entity Framework.
2.  Riga 4: L'istruzione using per lo spazio dei nomi **System.Threading.Tasks** consente di utilizzare il tipo **Task.**
3.  Riga 12 & 18: Stiamo catturando come attività che monitora lo stato di avanzamento di **PerformSomeDatabaseOperations** (riga 12) e quindi bloccando l'esecuzione del programma per questa attività per completare una volta che tutto il lavoro per il programma è fatto (riga 18).
4.  Riga 25: è stato aggiornato **PerformSomeDatabaseOperations** per essere contrassegnato come **asincrono** e restituire un **Task**.
5.  Riga 35: Stiamo chiamando la versione asincrona di SaveChanges e in attesa del suo completamento.
6.  Riga 42: Stiamo chiamando la versione asincrona di ToList e in attesa del risultato.

Per un elenco completo dei metodi di estensione disponibili nello spazio dei nomi System.Data.Entity, fare riferimento alla classe QueryableExtensions.For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class. *Sarà inoltre necessario aggiungere "using System.Data.Entity" alle istruzioni using.*

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

Ora che il codice è asincrono, è possibile osservare un flusso di esecuzione diverso quando si esegue il programma:Now that the code is asynchronous, we can observe a different execution flow when we run the program:

1. **SaveChanges** inizia a eseguire il push del nuovo **blog** nel database  
    *Una volta inviato il comando al database, non è necessario più tempo di calcolo nel thread gestito corrente. Il **PerformDatabaseOperations** metodo restituisce (anche se non ha terminato l'esecuzione) e il flusso del programma nel Metodo Main continua.*
2. **Citazione del giorno è scritto su Console**  
    *Poiché non vi è più lavoro da fare nel metodo Main, il thread gestito viene bloccato nella chiamata di attesa fino al completamento dell'operazione del database. Al termine, verrà eseguito il resto di **PerformDatabaseOperations.***
3.  **SaveChanges** viene completato  
4.  La query per tutti i **blog** viene inviata al database  
    *Anche in questo caso, il thread gestito è libero di eseguire altre operazioni mentre la query viene elaborata nel database. Poiché tutte le altre esecuzioni sono state completate, il thread si fermerà solo sulla chiamata Wait.*
5.  La query restituisce e i risultati vengono scritti **nella console**  

![Output asincronoAsync Output](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>L'asporto

Ora abbiamo visto quanto sia facile fare uso dei metodi asincroni di EF. Anche se i vantaggi di async potrebbero non essere molto evidenti con una semplice applicazione console, queste stesse strategie possono essere applicate in situazioni in cui le attività a esecuzione prolungata o associate alla rete potrebbero altrimenti bloccare l'applicazione o causare un numero elevato di thread per aumentare il footprint di memoria.
