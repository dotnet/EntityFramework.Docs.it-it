---
title: Registrazione semplice-EF Core
description: Registrazione da un EF Core DbContext con LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 076c4b12aa033b51a2b839686c520a76520ee415
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635614"
---
# <a name="simple-logging"></a><span data-ttu-id="abc64-103">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="abc64-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="abc64-104">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="abc64-104">This feature was introduced in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="abc64-105">È possibile [scaricare l'esempio di questo articolo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="abc64-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="abc64-106">È possibile utilizzare la registrazione semplice Entity Framework Core (EF Core) per ottenere facilmente i log durante lo sviluppo e il debug di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="abc64-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="abc64-107">Questa forma di registrazione richiede una configurazione minima e nessun pacchetto NuGet aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="abc64-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="abc64-108">EF Core si integra anche con [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), che richiede una maggiore configurazione, ma è spesso più adatto per la registrazione nelle applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="abc64-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="abc64-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="abc64-109">Configuration</span></span>

<span data-ttu-id="abc64-110">È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'utilizzo di <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> quando si [configura un'istanza di DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="abc64-110">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="abc64-111">Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="abc64-111">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="abc64-112">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-112">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="abc64-113">In alternativa, `LogTo` è possibile chiamare come parte di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o quando si crea un' <xref:Microsoft.EntityFrameworkCore.DbContextOptions> istanza da passare al `DbContext` costruttore.</span><span class="sxs-lookup"><span data-stu-id="abc64-113">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="abc64-114">Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring.</span><span class="sxs-lookup"><span data-stu-id="abc64-114">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="abc64-115">Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.</span><span class="sxs-lookup"><span data-stu-id="abc64-115">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="abc64-116">Indirizzamento dei log</span><span class="sxs-lookup"><span data-stu-id="abc64-116">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="abc64-117">Accesso alla console</span><span class="sxs-lookup"><span data-stu-id="abc64-117">Logging to the console</span></span>

<span data-ttu-id="abc64-118">`LogTo` richiede un <xref:System.Action%601> delegato che accetta una stringa.</span><span class="sxs-lookup"><span data-stu-id="abc64-118">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="abc64-119">EF Core chiamerà questo delegato con una stringa per ogni messaggio di log generato.</span><span class="sxs-lookup"><span data-stu-id="abc64-119">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="abc64-120">Fino al delegato viene quindi eseguita un'operazione con il messaggio specificato.</span><span class="sxs-lookup"><span data-stu-id="abc64-120">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="abc64-121">Il <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> metodo viene spesso usato per questo delegato, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="abc64-121">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="abc64-122">In questo modo, ogni messaggio di log viene scritto nella console.</span><span class="sxs-lookup"><span data-stu-id="abc64-122">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="abc64-123">Accesso alla finestra di debug</span><span class="sxs-lookup"><span data-stu-id="abc64-123">Logging to the debug window</span></span>

<span data-ttu-id="abc64-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> può essere usato per inviare l'output alla finestra di debug in Visual Studio o in altri IDE.</span><span class="sxs-lookup"><span data-stu-id="abc64-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="abc64-125">In questo caso è necessario usare la [sintassi lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) perché la `Debug` classe è compilata al di fuori delle build di rilascio.</span><span class="sxs-lookup"><span data-stu-id="abc64-125">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="abc64-126">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-126">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="abc64-127">Registrazione in un file</span><span class="sxs-lookup"><span data-stu-id="abc64-127">Logging to a file</span></span>

<span data-ttu-id="abc64-128">Per la scrittura in un file è necessario creare un <xref:System.IO.StreamWriter> o simile per il file.</span><span class="sxs-lookup"><span data-stu-id="abc64-128">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="abc64-129">Il <xref:System.IO.StreamWriter.WriteLine%2A> metodo può quindi essere usato come negli altri esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="abc64-129">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="abc64-130">Ricordarsi di assicurarsi che il file venga chiuso senza problemi eliminando il writer quando il contesto viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="abc64-130">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="abc64-131">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-131">For example:</span></span>

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true); 

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }
    
    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="abc64-132">Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per la registrazione in file nelle applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="abc64-132">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="abc64-133">Recupero di messaggi dettagliati</span><span class="sxs-lookup"><span data-stu-id="abc64-133">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="abc64-134">Dati sensibili</span><span class="sxs-lookup"><span data-stu-id="abc64-134">Sensitive data</span></span>

<span data-ttu-id="abc64-135">Per impostazione predefinita, EF Core non includerà i valori dei dati nei messaggi di eccezione.</span><span class="sxs-lookup"><span data-stu-id="abc64-135">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="abc64-136">Questo è dovuto al fatto che tali dati possono essere riservati e possono essere rivelati nell'uso in produzione se un'eccezione non viene gestita.</span><span class="sxs-lookup"><span data-stu-id="abc64-136">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="abc64-137">Tuttavia, la conoscenza dei valori dei dati, in particolare per le chiavi, può essere molto utile durante il debug.</span><span class="sxs-lookup"><span data-stu-id="abc64-137">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="abc64-138">Questa operazione può essere abilitata in EF Core chiamando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="abc64-138">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="abc64-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-139">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="abc64-140">Eccezioni di query dettagliate</span><span class="sxs-lookup"><span data-stu-id="abc64-140">Detailed query exceptions</span></span>

<span data-ttu-id="abc64-141">Per motivi di prestazioni, EF Core non esegue il wrapping di ogni chiamata per leggere un valore dal provider di database in un blocco try-catch.</span><span class="sxs-lookup"><span data-stu-id="abc64-141">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="abc64-142">Tuttavia, ciò comporta a volte eccezioni difficili da diagnosticare, specialmente quando il database restituisce un valore NULL quando non è consentito dal modello.</span><span class="sxs-lookup"><span data-stu-id="abc64-142">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="abc64-143">Se <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> si attiva, EF introdurrà i blocchi try-catch e fornirà quindi errori più dettagliati.</span><span class="sxs-lookup"><span data-stu-id="abc64-143">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="abc64-144">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-144">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="abc64-145">Filtro</span><span class="sxs-lookup"><span data-stu-id="abc64-145">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="abc64-146">Livelli di registrazione</span><span class="sxs-lookup"><span data-stu-id="abc64-146">Log levels</span></span>

<span data-ttu-id="abc64-147">Ogni messaggio del log EF Core viene assegnato a un livello definito dall' <xref:Microsoft.Extensions.Logging.LogLevel> enumerazione.</span><span class="sxs-lookup"><span data-stu-id="abc64-147">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="abc64-148">Per impostazione predefinita, EF Core registrazione semplice include tutti i messaggi a `Debug` livello o superiore.</span><span class="sxs-lookup"><span data-stu-id="abc64-148">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="abc64-149">`LogTo` è possibile passare un livello minimo superiore per filtrare alcuni messaggi.</span><span class="sxs-lookup"><span data-stu-id="abc64-149">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="abc64-150">Ad esempio, passando `Information` i risultati in un set minimo di log limitato all'accesso al database e ad alcuni messaggi di manutenzione.</span><span class="sxs-lookup"><span data-stu-id="abc64-150">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="abc64-151">Messaggi specifici</span><span class="sxs-lookup"><span data-stu-id="abc64-151">Specific messages</span></span>

<span data-ttu-id="abc64-152">A ogni messaggio del log viene assegnato un <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="abc64-152">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="abc64-153">È possibile accedere a questi ID dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe o dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> classe per i messaggi specifici relazionali.</span><span class="sxs-lookup"><span data-stu-id="abc64-153">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="abc64-154">Un provider di database può anche avere ID specifici del provider in una classe simile.</span><span class="sxs-lookup"><span data-stu-id="abc64-154">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="abc64-155">Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="abc64-155">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="abc64-156">`LogTo` può essere configurato in modo da registrare solo i messaggi associati a uno o più ID evento.</span><span class="sxs-lookup"><span data-stu-id="abc64-156">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="abc64-157">Ad esempio, per registrare solo i messaggi per il contesto inizializzato o eliminato:</span><span class="sxs-lookup"><span data-stu-id="abc64-157">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="abc64-158">Categorie di messaggi</span><span class="sxs-lookup"><span data-stu-id="abc64-158">Message categories</span></span>

<span data-ttu-id="abc64-159">Ogni messaggio di log viene assegnato a una categoria di logger gerarchici denominata.</span><span class="sxs-lookup"><span data-stu-id="abc64-159">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="abc64-160">Le categorie sono:</span><span class="sxs-lookup"><span data-stu-id="abc64-160">The categories are:</span></span>

| <span data-ttu-id="abc64-161">Category</span><span class="sxs-lookup"><span data-stu-id="abc64-161">Category</span></span>                                             | <span data-ttu-id="abc64-162">Messaggi</span><span class="sxs-lookup"><span data-stu-id="abc64-162">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="abc64-163">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="abc64-163">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="abc64-164">Tutti i messaggi di EF Core</span><span class="sxs-lookup"><span data-stu-id="abc64-164">All EF Core messages</span></span>
| <span data-ttu-id="abc64-165">Microsoft. EntityFrameworkCore. database</span><span class="sxs-lookup"><span data-stu-id="abc64-165">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="abc64-166">Tutte le interazioni con i database</span><span class="sxs-lookup"><span data-stu-id="abc64-166">All database interactions</span></span>
| <span data-ttu-id="abc64-167">Microsoft. EntityFrameworkCore. database. Connection</span><span class="sxs-lookup"><span data-stu-id="abc64-167">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="abc64-168">Usi di una connessione di database</span><span class="sxs-lookup"><span data-stu-id="abc64-168">Uses of a database connection</span></span>
| <span data-ttu-id="abc64-169">Microsoft. EntityFrameworkCore. database. Command</span><span class="sxs-lookup"><span data-stu-id="abc64-169">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="abc64-170">Usi di un comando di database</span><span class="sxs-lookup"><span data-stu-id="abc64-170">Uses of a database command</span></span>
| <span data-ttu-id="abc64-171">Microsoft. EntityFrameworkCore. database. Transaction</span><span class="sxs-lookup"><span data-stu-id="abc64-171">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="abc64-172">Utilizzi di una transazione di database</span><span class="sxs-lookup"><span data-stu-id="abc64-172">Uses of a database transaction</span></span>
| <span data-ttu-id="abc64-173">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="abc64-173">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="abc64-174">Salvataggio di entità, escluse le interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="abc64-174">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="abc64-175">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="abc64-175">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="abc64-176">Tutte le interazioni tra modelli e metadati</span><span class="sxs-lookup"><span data-stu-id="abc64-176">All model and metadata interactions</span></span>
| <span data-ttu-id="abc64-177">Microsoft. EntityFrameworkCore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="abc64-177">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="abc64-178">Convalida modello</span><span class="sxs-lookup"><span data-stu-id="abc64-178">Model validation</span></span>
| <span data-ttu-id="abc64-179">Microsoft. EntityFrameworkCore. query</span><span class="sxs-lookup"><span data-stu-id="abc64-179">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="abc64-180">Query, escluse le interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="abc64-180">Queries, excluding database interactions</span></span>
| <span data-ttu-id="abc64-181">Microsoft. EntityFrameworkCore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="abc64-181">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="abc64-182">Eventi generali, ad esempio creazione del contesto</span><span class="sxs-lookup"><span data-stu-id="abc64-182">General events, such as context creation</span></span>
| <span data-ttu-id="abc64-183">Microsoft. EntityFrameworkCore. impalcatura</span><span class="sxs-lookup"><span data-stu-id="abc64-183">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="abc64-184">reverse engineering database</span><span class="sxs-lookup"><span data-stu-id="abc64-184">Database reverse engineering</span></span>
| <span data-ttu-id="abc64-185">Microsoft. EntityFrameworkCore. Migrations</span><span class="sxs-lookup"><span data-stu-id="abc64-185">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="abc64-186">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="abc64-186">Migrations</span></span>
| <span data-ttu-id="abc64-187">Microsoft. EntityFrameworkCore. rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="abc64-187">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="abc64-188">Interazioni rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="abc64-188">Change tracking interactions</span></span>

<span data-ttu-id="abc64-189">`LogTo` può essere configurato in modo da registrare solo i messaggi da una o più categorie.</span><span class="sxs-lookup"><span data-stu-id="abc64-189">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="abc64-190">Ad esempio, per registrare solo le interazioni tra database:</span><span class="sxs-lookup"><span data-stu-id="abc64-190">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="abc64-191">Si noti che la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fornisce un'API gerarchica per trovare una categoria ed evita la necessità di utilizzare stringhe hardcoded.</span><span class="sxs-lookup"><span data-stu-id="abc64-191">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="abc64-192">Poiché le categorie sono gerarchiche, in questo esempio viene utilizzata la `Database` categoria per includere tutti i messaggi per le sottocategorie `Database.Connection` , `Database.Command` e `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="abc64-192">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="abc64-193">Filtri personalizzati</span><span class="sxs-lookup"><span data-stu-id="abc64-193">Custom filters</span></span>

<span data-ttu-id="abc64-194">`LogTo` consente di usare un filtro personalizzato per i casi in cui nessuna delle opzioni di filtro sopra indicate è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="abc64-194">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="abc64-195">Ad esempio, per registrare un messaggio a livello `Information` o superiore, oltre ai messaggi per l'apertura e la chiusura di una connessione:</span><span class="sxs-lookup"><span data-stu-id="abc64-195">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="abc64-196">Il filtro usando filtri personalizzati o una delle altre opzioni illustrate qui è più efficiente rispetto al filtro nel `LogTo` delegato.</span><span class="sxs-lookup"><span data-stu-id="abc64-196">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the `LogTo` delegate.</span></span> <span data-ttu-id="abc64-197">Questo perché se il filtro determina che il messaggio non deve essere registrato, il messaggio del log non viene ancora creato.</span><span class="sxs-lookup"><span data-stu-id="abc64-197">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="abc64-198">Configurazione per messaggi specifici</span><span class="sxs-lookup"><span data-stu-id="abc64-198">Configuration for specific messages</span></span>

<span data-ttu-id="abc64-199">L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF core consente alle applicazioni di modificare ciò che accade quando viene rilevato un evento specifico.</span><span class="sxs-lookup"><span data-stu-id="abc64-199">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="abc64-200">Questa operazione può essere utilizzata per:</span><span class="sxs-lookup"><span data-stu-id="abc64-200">This can be used to:</span></span>

* <span data-ttu-id="abc64-201">Modificare il livello di registrazione in cui viene registrato l'evento</span><span class="sxs-lookup"><span data-stu-id="abc64-201">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="abc64-202">Ignora la registrazione del tutto l'evento</span><span class="sxs-lookup"><span data-stu-id="abc64-202">Skip logging the event altogether</span></span>
* <span data-ttu-id="abc64-203">Genera un'eccezione quando si verifica l'evento</span><span class="sxs-lookup"><span data-stu-id="abc64-203">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="abc64-204">Modifica del livello di registrazione per un evento</span><span class="sxs-lookup"><span data-stu-id="abc64-204">Changing the log level for an event</span></span>

<span data-ttu-id="abc64-205">Nell'esempio precedente è stato usato un filtro personalizzato per registrare ogni messaggio in, oltre a `LogLevel.Information` due eventi definiti per `LogLevel.Debug` .</span><span class="sxs-lookup"><span data-stu-id="abc64-205">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="abc64-206">Per ottenere lo stesso risultato, è possibile modificare il livello di registrazione dei due `Debug` eventi in `Information` :</span><span class="sxs-lookup"><span data-stu-id="abc64-206">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="abc64-207">Annulla la registrazione di un evento</span><span class="sxs-lookup"><span data-stu-id="abc64-207">Suppress logging an event</span></span>

<span data-ttu-id="abc64-208">In modo analogo, un singolo evento può essere eliminato dalla registrazione.</span><span class="sxs-lookup"><span data-stu-id="abc64-208">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="abc64-209">Questa operazione è particolarmente utile per ignorare un avviso che è stato esaminato e compreso.</span><span class="sxs-lookup"><span data-stu-id="abc64-209">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="abc64-210">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-210">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="abc64-211">Genera per un evento</span><span class="sxs-lookup"><span data-stu-id="abc64-211">Throw for an event</span></span>

<span data-ttu-id="abc64-212">Infine, è possibile configurare EF Core per generare per un determinato evento.</span><span class="sxs-lookup"><span data-stu-id="abc64-212">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="abc64-213">Questa operazione è particolarmente utile per la modifica di un avviso in un errore.</span><span class="sxs-lookup"><span data-stu-id="abc64-213">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="abc64-214">(In effetti, si tratta dello scopo originale del `ConfigureWarnings` metodo, di conseguenza il nome). Per esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-214">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="abc64-215">Contenuto e formattazione del messaggio</span><span class="sxs-lookup"><span data-stu-id="abc64-215">Message contents and formatting</span></span>

<span data-ttu-id="abc64-216">Il contenuto predefinito da `LogTo` viene formattato su più righe.</span><span class="sxs-lookup"><span data-stu-id="abc64-216">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="abc64-217">La prima riga contiene i metadati del messaggio:</span><span class="sxs-lookup"><span data-stu-id="abc64-217">The first line contains message metadata:</span></span>

* <span data-ttu-id="abc64-218"><xref:Microsoft.Extensions.Logging.LogLevel>Come prefisso di quattro caratteri</span><span class="sxs-lookup"><span data-stu-id="abc64-218">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="abc64-219">Timestamp locale, formattato per le impostazioni cultura correnti</span><span class="sxs-lookup"><span data-stu-id="abc64-219">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="abc64-220">Oggetto <xref:Microsoft.Extensions.Logging.EventId> nel formato che può essere copiato/incollato per ottenere il membro da <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una delle altre `EventId` classi, più il valore ID non elaborato</span><span class="sxs-lookup"><span data-stu-id="abc64-220">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="abc64-221">Categoria di eventi, come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="abc64-221">The event category, as described above.</span></span>

<span data-ttu-id="abc64-222">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-222">For example:</span></span>

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

<span data-ttu-id="abc64-223">Questo contenuto può essere personalizzato passando i valori da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , come illustrato nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="abc64-223">This content can be customized by passing values from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions>, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="abc64-224">Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per un maggiore controllo sulla formattazione dei log.</span><span class="sxs-lookup"><span data-stu-id="abc64-224">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="abc64-225">Utilizzo dell'ora UTC</span><span class="sxs-lookup"><span data-stu-id="abc64-225">Using UTC time</span></span>

<span data-ttu-id="abc64-226">Per impostazione predefinita, i timestamp sono progettati per l'utilizzo locale durante il debug.</span><span class="sxs-lookup"><span data-stu-id="abc64-226">By default, timestamps are designed for local consumption while debugging.</span></span> <span data-ttu-id="abc64-227">Usare <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> invece per usare i timestamp UTC indipendenti dalle impostazioni cultura, ma è possibile conservarne tutti gli altri.</span><span class="sxs-lookup"><span data-stu-id="abc64-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="abc64-228">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-228">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="abc64-229">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="abc64-229">This example results in the following log formatting:</span></span>

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a><span data-ttu-id="abc64-230">Registrazione a riga singola</span><span class="sxs-lookup"><span data-stu-id="abc64-230">Single line logging</span></span>

<span data-ttu-id="abc64-231">A volte è utile ottenere esattamente una riga per ogni messaggio di log.</span><span class="sxs-lookup"><span data-stu-id="abc64-231">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="abc64-232">Questa operazione può essere abilitata da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="abc64-232">This can be enabled by <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType>.</span></span> <span data-ttu-id="abc64-233">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-233">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="abc64-234">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="abc64-234">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="abc64-235">Altre opzioni relative al contenuto</span><span class="sxs-lookup"><span data-stu-id="abc64-235">Other content options</span></span>

<span data-ttu-id="abc64-236">Gli altri flag in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> possono essere utilizzati per ridurre la quantità di metadati inclusi nel log.</span><span class="sxs-lookup"><span data-stu-id="abc64-236">Other flags in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="abc64-237">Questa operazione può essere utile in combinazione con la registrazione a riga singola.</span><span class="sxs-lookup"><span data-stu-id="abc64-237">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="abc64-238">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-238">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="abc64-239">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="abc64-239">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="abc64-240">Passaggio da EF6</span><span class="sxs-lookup"><span data-stu-id="abc64-240">Moving from EF6</span></span>

<span data-ttu-id="abc64-241">EF Core registrazione semplice differisce da <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in due modi importanti:</span><span class="sxs-lookup"><span data-stu-id="abc64-241">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="abc64-242">I messaggi di log non sono limitati solo alle interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="abc64-242">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="abc64-243">La registrazione deve essere configurata in fase di inizializzazione del contesto</span><span class="sxs-lookup"><span data-stu-id="abc64-243">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="abc64-244">Per la prima differenza, è possibile utilizzare il filtro descritto in precedenza per limitare i messaggi che vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="abc64-244">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="abc64-245">La seconda differenza è una modifica intenzionale che consente di migliorare le prestazioni senza generare messaggi di log quando non sono necessari.</span><span class="sxs-lookup"><span data-stu-id="abc64-245">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="abc64-246">Tuttavia, è comunque possibile ottenere un comportamento simile a EF6 creando una `Log` Proprietà in `DbContext` e quindi usandola solo quando è stata impostata.</span><span class="sxs-lookup"><span data-stu-id="abc64-246">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="abc64-247">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="abc64-247">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
