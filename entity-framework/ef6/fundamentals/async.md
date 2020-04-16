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
# <a name="async-query-and-save"></a><span data-ttu-id="7ff7a-102">Query asincrona e salvataggio</span><span class="sxs-lookup"><span data-stu-id="7ff7a-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="7ff7a-103">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7ff7a-104">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="7ff7a-105">EF6 ha introdotto il supporto per la query asincrona e il salvataggio utilizzando le [parole chiave async e await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) che sono state introdotte in .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="7ff7a-106">Sebbene non tutte le applicazioni possano trarre vantaggio dall'asincronia, può essere utilizzata per migliorare la velocità di risposta dei client e la scalabilità del server durante la gestione di attività a esecuzione prolungata, di rete o associate a I/O.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="7ff7a-107">Quando usare veramente asyncWhen to really use async</span><span class="sxs-lookup"><span data-stu-id="7ff7a-107">When to really use async</span></span>

<span data-ttu-id="7ff7a-108">Lo scopo di questa procedura dettagliata è introdurre i concetti asincroni in modo da semplificare l'osservazione della differenza tra l'esecuzione asincrona e sincrona del programma.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="7ff7a-109">Questa procedura dettagliata non ha lo scopo di illustrare uno degli scenari chiave in cui la programmazione asincrona offre vantaggi.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="7ff7a-110">La programmazione asincrona si concentra principalmente sulla liberazione del thread gestito corrente (thread che esegue codice .NET) per eseguire altre operazioni durante l'attesa di un'operazione che non richiede alcun tempo di calcolo da un thread gestito.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="7ff7a-111">Ad esempio, mentre il motore di database sta elaborando una query non c'è nulla da fare dal codice .NET.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="7ff7a-112">Nelle applicazioni client (WinForms, WPF e così via) il thread corrente può essere utilizzato per mantenere l'interfaccia utente reattiva durante l'esecuzione dell'operazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="7ff7a-113">Nelle applicazioni server (ASP.NET e così via) il thread può essere utilizzato per elaborare altre richieste in ingresso, in grado di ridurre l'utilizzo della memoria e/o aumentare la velocità effettiva del server.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="7ff7a-114">Nella maggior parte delle applicazioni che utilizzano async non avrà vantaggi evidenti e anche potrebbe essere dannoso.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="7ff7a-115">Usare test, profilatura e buon senso per misurare l'impatto di async nello scenario specifico prima di eseguirne il commit.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="7ff7a-116">Ecco alcune altre risorse da acquisire informazioni su async:Here are some more resources to learn about async:</span><span class="sxs-lookup"><span data-stu-id="7ff7a-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="7ff7a-117">Panoramica di Brandon Bray su async/await in .NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7ff7a-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="7ff7a-118">[Pagine di programmazione asincrona](https://msdn.microsoft.com/library/hh191443.aspx) in MSDN Library</span><span class="sxs-lookup"><span data-stu-id="7ff7a-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="7ff7a-119">[Come compilare ASP.NET applicazioni Web utilizzando Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (include una dimostrazione di aumento della velocità effettiva del server)How to Build ASP.NET Web Applications Using Async (includes a demo of a increased server throughput)</span><span class="sxs-lookup"><span data-stu-id="7ff7a-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="7ff7a-120">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="7ff7a-120">Create the model</span></span>

<span data-ttu-id="7ff7a-121">Utilizzeremo il flusso di [lavoro Code First](~/ef6/modeling/code-first/workflows/new-database.md) per creare il modello e generare il database, tuttavia la funzionalità asincrona funzionerà con tutti i modelli di EF, inclusi quelli creati con la finestra di progettazione di EF.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="7ff7a-122">Creare un'applicazione console e chiamarla **AsyncDemoCreate** a Console Application and call it AsyncDemo</span><span class="sxs-lookup"><span data-stu-id="7ff7a-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="7ff7a-123">Aggiungere il pacchetto NuGet di EntityFrameworkAdd the EntityFramework NuGet package</span><span class="sxs-lookup"><span data-stu-id="7ff7a-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="7ff7a-124">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AsyncDemoIn** Solution Explorer, right-click on the AsyncDemo project</span><span class="sxs-lookup"><span data-stu-id="7ff7a-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="7ff7a-125">Selezionare **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="7ff7a-126">Nella finestra di dialogo Gestisci pacchetti NuGet, selezionare la scheda **Online** e scegliere il pacchetto **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="7ff7a-127">Fare clic su **Installa**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-127">Click **Install**</span></span>
-   <span data-ttu-id="7ff7a-128">Aggiungere una classe **Model.cs** con l'implementazione seguenteAdd a Model.cs class with the following implementation</span><span class="sxs-lookup"><span data-stu-id="7ff7a-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="7ff7a-129">Creare un programma sincrono</span><span class="sxs-lookup"><span data-stu-id="7ff7a-129">Create a synchronous program</span></span>

<span data-ttu-id="7ff7a-130">Ora che abbiamo un modello di EF, è possibile scrivere codice che lo usa per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="7ff7a-131">Sostituire il contenuto di **Program.cs** con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="7ff7a-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="7ff7a-132">Questo codice chiama il metodo **PerformDatabaseOperations** che salva un nuovo **oggetto Blog** nel database, quindi recupera tutti i **Blog** dal database e li stampa **nella console**.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="7ff7a-133">Dopo questo, il programma scrive una citazione del giorno alla **Console**.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="7ff7a-134">Poiché il codice è sincrono, è possibile osservare il seguente flusso di esecuzione quando si esegue il programma:Since the code is synchronous, we can observe the following execution flow when we run the program:</span><span class="sxs-lookup"><span data-stu-id="7ff7a-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="7ff7a-135">**SaveChanges** inizia a eseguire il push del nuovo **blog** nel database</span><span class="sxs-lookup"><span data-stu-id="7ff7a-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="7ff7a-136">**SaveChanges** viene completato</span><span class="sxs-lookup"><span data-stu-id="7ff7a-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="7ff7a-137">La query per tutti i **blog** viene inviata al database</span><span class="sxs-lookup"><span data-stu-id="7ff7a-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="7ff7a-138">La query restituisce e i risultati vengono scritti **nella console**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="7ff7a-139">Citazione del giorno è scritto su **Console**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-139">Quote of the day is written to **Console**</span></span>

![Uscita di sincronizzazione](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="7ff7a-141">Renderlo asincrono</span><span class="sxs-lookup"><span data-stu-id="7ff7a-141">Making it asynchronous</span></span>

<span data-ttu-id="7ff7a-142">Ora che abbiamo il nostro programma in funzione, possiamo iniziare a fare uso del nuovo async e attendere parole chiave.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="7ff7a-143">Sono state apportate le seguenti modifiche aProgram.cs</span><span class="sxs-lookup"><span data-stu-id="7ff7a-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="7ff7a-144">Riga 2: L'istruzione using per lo spazio dei nomi **System.Data.Entity** consente di accedere ai metodi di estensione asincrona di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="7ff7a-145">Riga 4: L'istruzione using per lo spazio dei nomi **System.Threading.Tasks** consente di utilizzare il tipo **Task.**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="7ff7a-146">Riga 12 & 18: Stiamo catturando come attività che monitora lo stato di avanzamento di **PerformSomeDatabaseOperations** (riga 12) e quindi bloccando l'esecuzione del programma per questa attività per completare una volta che tutto il lavoro per il programma è fatto (riga 18).</span><span class="sxs-lookup"><span data-stu-id="7ff7a-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="7ff7a-147">Riga 25: è stato aggiornato **PerformSomeDatabaseOperations** per essere contrassegnato come **asincrono** e restituire un **Task**.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="7ff7a-148">Riga 35: Stiamo chiamando la versione asincrona di SaveChanges e in attesa del suo completamento.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="7ff7a-149">Riga 42: Stiamo chiamando la versione asincrona di ToList e in attesa del risultato.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="7ff7a-150">Per un elenco completo dei metodi di estensione disponibili nello spazio dei nomi System.Data.Entity, fare riferimento alla classe QueryableExtensions.For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="7ff7a-151">*Sarà inoltre necessario aggiungere "using System.Data.Entity" alle istruzioni using.*</span><span class="sxs-lookup"><span data-stu-id="7ff7a-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="7ff7a-152">Ora che il codice è asincrono, è possibile osservare un flusso di esecuzione diverso quando si esegue il programma:Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span><span class="sxs-lookup"><span data-stu-id="7ff7a-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="7ff7a-153">**SaveChanges** inizia a eseguire il push del nuovo **blog** nel database</span><span class="sxs-lookup"><span data-stu-id="7ff7a-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="7ff7a-154">*Una volta inviato il comando al database, non è necessario più tempo di calcolo nel thread gestito corrente. Il **PerformDatabaseOperations** metodo restituisce (anche se non ha terminato l'esecuzione) e il flusso del programma nel Metodo Main continua.*</span><span class="sxs-lookup"><span data-stu-id="7ff7a-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="7ff7a-155">**Citazione del giorno è scritto su Console**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="7ff7a-156">*Poiché non vi è più lavoro da fare nel metodo Main, il thread gestito viene bloccato nella chiamata di attesa fino al completamento dell'operazione del database. Al termine, verrà eseguito il resto di **PerformDatabaseOperations.***</span><span class="sxs-lookup"><span data-stu-id="7ff7a-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="7ff7a-157">**SaveChanges** viene completato</span><span class="sxs-lookup"><span data-stu-id="7ff7a-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="7ff7a-158">La query per tutti i **blog** viene inviata al database</span><span class="sxs-lookup"><span data-stu-id="7ff7a-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="7ff7a-159">*Anche in questo caso, il thread gestito è libero di eseguire altre operazioni mentre la query viene elaborata nel database. Poiché tutte le altre esecuzioni sono state completate, il thread si fermerà solo sulla chiamata Wait.*</span><span class="sxs-lookup"><span data-stu-id="7ff7a-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="7ff7a-160">La query restituisce e i risultati vengono scritti **nella console**</span><span class="sxs-lookup"><span data-stu-id="7ff7a-160">Query returns and results are written to **Console**</span></span>  

![Output asincronoAsync Output](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="7ff7a-162">L'asporto</span><span class="sxs-lookup"><span data-stu-id="7ff7a-162">The takeaway</span></span>

<span data-ttu-id="7ff7a-163">Ora abbiamo visto quanto sia facile fare uso dei metodi asincroni di EF.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="7ff7a-164">Anche se i vantaggi di async potrebbero non essere molto evidenti con una semplice applicazione console, queste stesse strategie possono essere applicate in situazioni in cui le attività a esecuzione prolungata o associate alla rete potrebbero altrimenti bloccare l'applicazione o causare un numero elevato di thread per aumentare il footprint di memoria.</span><span class="sxs-lookup"><span data-stu-id="7ff7a-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
