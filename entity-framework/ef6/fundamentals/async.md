---
title: Query asincrona e Save-EF6
description: Query asincrona e salvataggio in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 77204f56e4dca63322c8ae2e1117318262f16f83
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635731"
---
# <a name="async-query-and-save"></a>Query asincrona e salvataggio
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

EF6 ha introdotto il supporto per la query asincrona e il salvataggio usando le [parole chiave async e await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) introdotte in .NET 4,5. Sebbene non tutte le applicazioni possano trarre vantaggio da modalità asincrona, possono essere usate per migliorare la velocità di risposta dei client e la scalabilità del server quando si gestiscono attività a esecuzione prolungata, di rete o di I/O.

## <a name="when-to-really-use-async"></a>Quando usare realmente Async

Lo scopo di questa procedura dettagliata è quello di introdurre i concetti asincroni in modo da semplificare l'osservazione della differenza tra l'esecuzione asincrona e sincrona del programma. Questa procedura dettagliata non è stata progettata per illustrare gli scenari principali in cui la programmazione asincrona offre vantaggi.

La programmazione asincrona si concentra principalmente sulla liberazione del thread gestito corrente (thread che esegue il codice .NET) per eseguire altre operazioni durante l'attesa di un'operazione che non richiede alcun tempo di calcolo da un thread gestito. Ad esempio, mentre il motore di database elabora una query, non è necessario eseguire alcuna operazione dal codice .NET.

Nelle applicazioni client (WinForms, WPF e così via) il thread corrente può essere usato per assicurare la velocità di risposta dell'interfaccia utente mentre viene eseguita l'operazione asincrona. Nelle applicazioni server (ASP.NET e così via) il thread può essere usato per elaborare altre richieste in ingresso. in questo modo è possibile ridurre l'utilizzo della memoria e/o aumentare la velocità effettiva del server.

Nella maggior parte delle applicazioni, l'uso di Async non avrà vantaggi evidenti e anche potrebbe essere dannoso. Usare i test, la profilatura e il senso comune per misurare l'effetto di Async in uno scenario specifico prima di eseguire il commit.

Di seguito sono riportate alcune risorse per ottenere informazioni su Async:

-   [Panoramica di Brandon Bray su async/await in .NET 4,5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   Pagine di [programmazione asincrone](https://msdn.microsoft.com/library/hh191443.aspx) in MSDN Library
-   [Come compilare applicazioni Web ASP.NET usando Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (include una demo dell'aumento della velocità effettiva del server)

## <a name="create-the-model"></a>Creare il modello

Il flusso di lavoro [Code First](xref:ef6/modeling/code-first/workflows/new-database) verrà usato per creare il modello e generare il database. la funzionalità asincrona, tuttavia, funzionerà con tutti i modelli EF, inclusi quelli creati con la finestra di progettazione EF.

-   Creare un'applicazione console e chiamarla **AsyncDemo**
-   Aggiungere il pacchetto NuGet EntityFramework
    -   In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AsyncDemo**
    -   Selezionare **Gestisci pacchetti NuGet...**
    -   Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework**
    -   Fai clic su **Install** (Installa).
-   Aggiungere una classe **Model.cs** con l'implementazione seguente

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

 

## <a name="create-a-synchronous-program"></a>Creazione di un programma sincrono

Ora che è disponibile un modello EF, è possibile scrivere il codice che lo usa per eseguire l'accesso ai dati.

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

Questo codice chiama il `PerformDatabaseOperations` metodo che salva un nuovo **Blog** nel database e quindi recupera tutti i **Blog** dal database e li stampa nella **console**. Al termine di questa operazione, il programma scrive un'offerta del giorno nella **console**.

Poiché il codice è sincrono, è possibile osservare il flusso di esecuzione seguente quando si esegue il programma:

1.  `SaveChanges` inizia a eseguire il push del nuovo **Blog** nel database
2.  `SaveChanges` completa
3.  La query per tutti i **Blog** viene inviata al database
4.  La query restituisce e i risultati vengono scritti nella **console**
5.  La citazione del giorno viene scritta nella **console**

![Output di sincronizzazione](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Esecuzione asincrona

Ora che il programma è in esecuzione, è possibile iniziare a usare le nuove parole chiave async e await. Sono state apportate le modifiche seguenti a Program.cs

1.  Riga 2: l'istruzione using per lo `System.Data.Entity` spazio dei nomi consente di accedere ai metodi di estensione EF Async.
2.  Riga 4: l'istruzione using per lo `System.Threading.Tasks` spazio dei nomi consente di usare il `Task` tipo.
3.  Riga 12 & 18: viene eseguita l'acquisizione come attività che monitora lo stato di avanzamento di `PerformSomeDatabaseOperations` (riga 12) e quindi blocca l'esecuzione del programma per completare questa attività una volta completate tutte le operazioni del programma (riga 18).
4.  Riga 25: è stato aggiornato `PerformSomeDatabaseOperations` per essere contrassegnato come `async` e viene restituito `Task` .
5.  Riga 35: viene ora chiamata la versione asincrona di `SaveChanges` e in attesa del completamento.
6.  Riga 42: viene ora chiamata la versione asincrona di `ToList` e in attesa del risultato.

Per un elenco completo dei metodi di estensione disponibili nello `System.Data.Entity` spazio dei nomi, fare riferimento alla `QueryableExtensions` classe. *Sarà inoltre necessario aggiungere `using System.Data.Entity` le istruzioni using.*

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

Ora che il codice è asincrono, è possibile osservare un flusso di esecuzione diverso durante l'esecuzione del programma:

1. `SaveChanges` inizia a eseguire il push del nuovo **Blog** nel database  
    *Quando il comando viene inviato al database, non è necessario più tempo di calcolo nel thread gestito corrente. Il `PerformDatabaseOperations` metodo restituisce (anche se l'esecuzione non è terminata) e il flusso di programma nel metodo Main continua.*
2. **La citazione del giorno viene scritta nella console**  
    *Poiché non vi sono altre operazioni da eseguire nel metodo Main, il thread gestito viene bloccato sulla `Wait` chiamata fino al completamento dell'operazione di database. Al termine, il resto del `PerformDatabaseOperations` verrà eseguito.*
3.  `SaveChanges` completa  
4.  La query per tutti i **Blog** viene inviata al database  
    *Anche in questo caso, il thread gestito è libero di eseguire altre operazioni mentre la query viene elaborata nel database. Poiché tutte le altre esecuzioni sono state completate, il thread si interromperà solo sulla chiamata di attesa.*
5.  La query restituisce e i risultati vengono scritti nella **console**  

![Output asincrono](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Da asporto

Ora abbiamo visto quanto sia facile usare i metodi asincroni di EF. Sebbene i vantaggi di Async potrebbero non essere molto evidenti con una semplice app console, le stesse strategie possono essere applicate nelle situazioni in cui le attività a esecuzione prolungata o in rete potrebbero bloccare l'applicazione o causare un numero elevato di thread per aumentare il footprint di memoria.
