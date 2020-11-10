---
title: Uso di Microsoft. Extensions. Logging-EF Core
description: Registrazione da EF Core utilizzando Microsoft. Extensions. Logging in ASP.NET Core e altri tipi di applicazioni
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431535"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="3ac7e-103">Utilizzo di Microsoft. Extensions. Logging in EF Core</span><span class="sxs-lookup"><span data-stu-id="3ac7e-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="3ac7e-104">[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) è un meccanismo di registrazione estendibile con provider plug-in per molti sistemi di registrazione comuni.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="3ac7e-105">I plug-in forniti da Microsoft (ad esempio [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) e plug-in di terze parti (ad esempio, [Serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sono disponibili come pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="3ac7e-106">Entity Framework Core (EF Core) si integra completamente con `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="3ac7e-107">Tuttavia, si consiglia di usare la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) per un modo più semplice di registrare, in particolare per le applicazioni che non usano l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="3ac7e-108">Applicazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac7e-108">ASP.NET Core applications</span></span>

<span data-ttu-id="3ac7e-109">`Microsoft.Extensions.Logging` viene [usato per impostazione predefinita nelle applicazioni ASP.NET Core](/aspnet/core/fundamentals/logging).</span><span class="sxs-lookup"><span data-stu-id="3ac7e-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="3ac7e-110">Chiamata di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="3ac7e-111">Altri tipi di applicazioni</span><span class="sxs-lookup"><span data-stu-id="3ac7e-111">Other application types</span></span>

<span data-ttu-id="3ac7e-112">Altri tipi di applicazioni possono usare [GenericHost](/dotnet/core/extensions/generic-host) per ottenere gli stessi modelli di inserimento delle dipendenze usati in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="3ac7e-113">È possibile usare AddDbContext o AddDbContextPool come nelle applicazioni ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="3ac7e-114">`Microsoft.Extensions.Logging` può essere utilizzato anche per le applicazioni che non utilizzano l'inserimento di dipendenze, sebbene la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) possa essere più semplice da configurare.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="3ac7e-115">`Microsoft.Extensions.Logging` richiede la creazione di un oggetto <xref:Microsoft.Extensions.Logging.LoggerFactory> .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="3ac7e-116">Questa factory deve essere archiviata come istanza statica/globale in un punto e usata ogni volta che viene creato un DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="3ac7e-117">Ad esempio, è normale archiviare la factory del logger come proprietà statica in DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="3ac7e-118">EF Core 3,0 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="3ac7e-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="3ac7e-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="3ac7e-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="3ac7e-120">In EF Core 2,1, è molto importante che le applicazioni non creino una nuova istanza di LoggerFactory per ogni istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="3ac7e-121">Questa operazione comporterà una perdita di memoria e una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="3ac7e-122">Questo problema è stato risolto in EF Core 3,0 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="3ac7e-123">Questa istanza singleton/globale deve quindi essere registrata con EF Core su <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="3ac7e-124">Esempio:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="3ac7e-125">Recupero di messaggi dettagliati</span><span class="sxs-lookup"><span data-stu-id="3ac7e-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="3ac7e-126">Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="3ac7e-127">Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="3ac7e-128">Dati sensibili</span><span class="sxs-lookup"><span data-stu-id="3ac7e-128">Sensitive data</span></span>

<span data-ttu-id="3ac7e-129">Per impostazione predefinita, EF Core non includerà i valori dei dati nei messaggi di eccezione.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="3ac7e-130">Questo è dovuto al fatto che tali dati possono essere riservati e possono essere rivelati nell'uso in produzione se un'eccezione non viene gestita.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="3ac7e-131">Tuttavia, la conoscenza dei valori dei dati, in particolare per le chiavi, può essere molto utile durante il debug.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="3ac7e-132">Questa operazione può essere abilitata in EF Core chiamando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="3ac7e-133">Esempio:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="3ac7e-134">Eccezioni di query dettagliate</span><span class="sxs-lookup"><span data-stu-id="3ac7e-134">Detailed query exceptions</span></span>

<span data-ttu-id="3ac7e-135">Per motivi di prestazioni, EF Core non esegue il wrapping di ogni chiamata per leggere un valore dal provider di database in un blocco try-catch.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="3ac7e-136">Tuttavia, ciò comporta a volte eccezioni difficili da diagnosticare, specialmente quando il database restituisce un valore NULL quando non è consentito dal modello.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="3ac7e-137">Se <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> si attiva, EF introdurrà i blocchi try-catch e fornirà quindi errori più dettagliati.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="3ac7e-138">Esempio:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="3ac7e-139">Configurazione per messaggi specifici</span><span class="sxs-lookup"><span data-stu-id="3ac7e-139">Configuration for specific messages</span></span>

<span data-ttu-id="3ac7e-140">L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF core consente alle applicazioni di modificare ciò che accade quando viene rilevato un evento specifico.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="3ac7e-141">Questa operazione può essere utilizzata per:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-141">This can be used to:</span></span>

* <span data-ttu-id="3ac7e-142">Modificare il livello di registrazione in cui viene registrato l'evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="3ac7e-143">Ignora la registrazione del tutto l'evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="3ac7e-144">Genera un'eccezione quando si verifica l'evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="3ac7e-145">Modifica del livello di registrazione per un evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-145">Changing the log level for an event</span></span>

<span data-ttu-id="3ac7e-146">In alcuni casi può essere utile modificare il livello di registrazione predefinito per un evento.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="3ac7e-147">Ad esempio, può essere usato per innalzare di livello due eventi aggiuntivi da `LogLevel.Debug` a `LogLevel.Information` :</span><span class="sxs-lookup"><span data-stu-id="3ac7e-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="3ac7e-148">Annulla la registrazione di un evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-148">Suppress logging an event</span></span>

<span data-ttu-id="3ac7e-149">In modo analogo, un singolo evento può essere eliminato dalla registrazione.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="3ac7e-150">Questa operazione è particolarmente utile per ignorare un avviso che è stato esaminato e compreso.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="3ac7e-151">Esempio:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="3ac7e-152">Genera per un evento</span><span class="sxs-lookup"><span data-stu-id="3ac7e-152">Throw for an event</span></span>

<span data-ttu-id="3ac7e-153">Infine, è possibile configurare EF Core per generare per un determinato evento.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="3ac7e-154">Questa operazione è particolarmente utile per la modifica di un avviso in un errore.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="3ac7e-155">(In effetti, si tratta dello scopo originale del `ConfigureWarnings` metodo, di conseguenza il nome). Per esempio:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="3ac7e-156">Filtro e altre configurazioni</span><span class="sxs-lookup"><span data-stu-id="3ac7e-156">Filtering and other configuration</span></span>

<span data-ttu-id="3ac7e-157">Vedere [registrazione in .NET](/dotnet/core/extensions/logging) per informazioni aggiuntive sul filtro dei log e altre configurazioni.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="3ac7e-158">Gli eventi di registrazione EF Core sono definiti in uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="3ac7e-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="3ac7e-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> per gli eventi comuni a tutti i provider di database EF Core</span><span class="sxs-lookup"><span data-stu-id="3ac7e-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="3ac7e-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> per gli eventi comuni a tutti i provider di database relazionali</span><span class="sxs-lookup"><span data-stu-id="3ac7e-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="3ac7e-161">Classe simile per gli eventi specifici del provider di database corrente.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="3ac7e-162">Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3ac7e-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="3ac7e-163">Queste definizioni contengono gli ID evento, il livello di log e la categoria per ogni evento, come usato da `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="3ac7e-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
