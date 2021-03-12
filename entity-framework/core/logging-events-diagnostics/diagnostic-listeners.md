---
title: Uso di listener di diagnostica-EF Core
description: Uso di DiagnosticListener per il consumo globale di EF Core Diagnostics
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: 7035acaa6d306e73a0a2c071532ece0d8e9a0a1d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024251"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="659ff-103">Uso di listener di diagnostica in EF Core</span><span class="sxs-lookup"><span data-stu-id="659ff-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="659ff-104">È possibile [scaricare l'esempio di questo articolo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="659ff-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="659ff-105">I listener di diagnostica consentono l'ascolto di qualsiasi evento EF Core che si verifica nel processo .NET corrente.</span><span class="sxs-lookup"><span data-stu-id="659ff-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="659ff-106">La <xref:System.Diagnostics.DiagnosticListener> classe fa parte di un [meccanismo comune in .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) per ottenere informazioni di diagnostica dalle applicazioni in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="659ff-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="659ff-107">I listener di diagnostica non sono adatti per ottenere gli eventi da una singola istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="659ff-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="659ff-108">EF Core gli [intercettori](xref:core/logging-events-diagnostics/interceptors) forniscono l'accesso agli stessi eventi con registrazione per contesto.</span><span class="sxs-lookup"><span data-stu-id="659ff-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="659ff-109">I listener di diagnostica non sono progettati per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="659ff-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="659ff-110">Si consiglia di usare la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="659ff-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="659ff-111">Esempio: osservazione degli eventi di diagnostica</span><span class="sxs-lookup"><span data-stu-id="659ff-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="659ff-112">La risoluzione di eventi EF Core è un processo in due passaggi.</span><span class="sxs-lookup"><span data-stu-id="659ff-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="659ff-113">Innanzitutto, è necessario creare un [Observer](/dotnet/standard/events/observer-design-pattern) per `DiagnosticListener` se stesso:</span><span class="sxs-lookup"><span data-stu-id="659ff-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

<span data-ttu-id="659ff-114">Il `OnNext` metodo cerca il DiagnosticListener che deriva da EF core.</span><span class="sxs-lookup"><span data-stu-id="659ff-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="659ff-115">Questo listener ha il nome "Microsoft. EntityFrameworkCore", che può essere ottenuto dalla <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe come illustrato.</span><span class="sxs-lookup"><span data-stu-id="659ff-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="659ff-116">Questo Observer deve quindi essere registrato a livello globale, ad esempio nel metodo dell'applicazione `Main` :</span><span class="sxs-lookup"><span data-stu-id="659ff-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="659ff-117">In secondo luogo, una volta individuato il EF Core DiagnosticListener, viene creato un nuovo Observer chiave-valore per sottoscrivere gli eventi EF Core effettivi.</span><span class="sxs-lookup"><span data-stu-id="659ff-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="659ff-118">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="659ff-118">For example:</span></span>

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

<span data-ttu-id="659ff-119">Il `OnNext` metodo è questa volta chiamato con una coppia chiave/valore per ogni evento EF core.</span><span class="sxs-lookup"><span data-stu-id="659ff-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="659ff-120">La chiave è il nome dell'evento, che può essere ottenuto da uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="659ff-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="659ff-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> per gli eventi comuni a tutti i provider di database EF Core</span><span class="sxs-lookup"><span data-stu-id="659ff-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="659ff-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> per gli eventi comuni a tutti i provider di database relazionali</span><span class="sxs-lookup"><span data-stu-id="659ff-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="659ff-123">Classe simile per gli eventi specifici del provider di database corrente.</span><span class="sxs-lookup"><span data-stu-id="659ff-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="659ff-124">Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="659ff-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="659ff-125">Il valore della coppia chiave/valore è un tipo di payload specifico dell'evento.</span><span class="sxs-lookup"><span data-stu-id="659ff-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="659ff-126">Il tipo di payload da prevedere è documentato su ogni evento definito in queste classi di evento.</span><span class="sxs-lookup"><span data-stu-id="659ff-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="659ff-127">Il codice precedente, ad esempio, gestisce gli <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> eventi e.</span><span class="sxs-lookup"><span data-stu-id="659ff-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="659ff-128">Per la prima di queste, il payload è <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> .</span><span class="sxs-lookup"><span data-stu-id="659ff-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="659ff-129">Per il secondo, è <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .</span><span class="sxs-lookup"><span data-stu-id="659ff-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="659ff-130">Viene eseguito l'override di ToString in ogni classe di dati di evento EF Core per generare il messaggio di log equivalente per l'evento.</span><span class="sxs-lookup"><span data-stu-id="659ff-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="659ff-131">Ad esempio, la chiamata di `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inizializzato" BlogsContext "utilizzando il provider" Microsoft. EntityFrameworkCore. sqlite "con options: None".</span><span class="sxs-lookup"><span data-stu-id="659ff-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="659ff-132">L' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contiene una semplice applicazione console che apporta modifiche al database blogging e stampa gli eventi di diagnostica rilevati.</span><span class="sxs-lookup"><span data-stu-id="659ff-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion

        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

<span data-ttu-id="659ff-133">L'output di questo codice Mostra gli eventi rilevati:</span><span class="sxs-lookup"><span data-stu-id="659ff-133">The output from this code shows the events detected:</span></span>

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
