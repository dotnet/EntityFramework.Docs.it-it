---
title: Registrazione semplice-EF Core
description: Registrazione da un DbContext EFCore tramite LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/miscellaneous/events/simple-logging
ms.openlocfilehash: 2d671dd9daad16a6431b91419736ec67733e41b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066613"
---
# <a name="simple-logging"></a><span data-ttu-id="46150-103">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="46150-103">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="46150-104">Questa funzionalità è stata aggiunta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="46150-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="46150-105">È possibile [visualizzare e scaricare l'esempio di questo articolo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="46150-105">You can [view and download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) on GitHub.</span></span>

<span data-ttu-id="46150-106">È possibile utilizzare la registrazione semplice Entity Framework Core (EF Core) per ottenere facilmente i log durante lo sviluppo e il debug di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="46150-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="46150-107">Questa forma di registrazione richiede una configurazione minima e nessun pacchetto NuGet aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="46150-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="46150-108">EF Core si integra anche con [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging), che richiede una maggiore configurazione, ma è spesso più adatto per la registrazione nelle applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="46150-108">EF Core also integrates with [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="46150-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="46150-109">Configuration</span></span>

<span data-ttu-id="46150-110">È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'uso di [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="46150-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="46150-111">Quando si [configura un'istanza di DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="46150-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="46150-112">Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="46150-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="46150-113">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="46150-114">In alternativa, `LogTo` è possibile chiamare come parte di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o quando si crea un' <xref:Microsoft.EntityFrameworkCore.DbContextOptions> istanza da passare al `DbContext` costruttore.</span><span class="sxs-lookup"><span data-stu-id="46150-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="46150-115">Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring.</span><span class="sxs-lookup"><span data-stu-id="46150-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="46150-116">Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.</span><span class="sxs-lookup"><span data-stu-id="46150-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="46150-117">Indirizzamento dei log</span><span class="sxs-lookup"><span data-stu-id="46150-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="46150-118">Accesso alla console</span><span class="sxs-lookup"><span data-stu-id="46150-118">Logging to the console</span></span>

<span data-ttu-id="46150-119">`LogTo` richiede un <xref:System.Action%601> delegato che accetta una stringa.</span><span class="sxs-lookup"><span data-stu-id="46150-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="46150-120">EF Core chiamerà questo delegato con una stringa per ogni messaggio di log generato.</span><span class="sxs-lookup"><span data-stu-id="46150-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="46150-121">Fino al delegato viene quindi eseguita un'operazione con il messaggio specificato.</span><span class="sxs-lookup"><span data-stu-id="46150-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="46150-122">Il <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> metodo viene spesso usato per questo delegato, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="46150-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="46150-123">In questo modo, ogni messaggio di log viene scritto nella console.</span><span class="sxs-lookup"><span data-stu-id="46150-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="46150-124">Accesso alla finestra di debug</span><span class="sxs-lookup"><span data-stu-id="46150-124">Logging to the debug window</span></span>

<span data-ttu-id="46150-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> può essere usato per inviare l'output alla finestra di debug in Visual Studio o in altri IDE.</span><span class="sxs-lookup"><span data-stu-id="46150-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="46150-126">In questo caso è necessario usare la [sintassi lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) perché la `Debug` classe è compilata al di fuori delle build di rilascio.</span><span class="sxs-lookup"><span data-stu-id="46150-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="46150-127">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="46150-128">Registrazione in un file</span><span class="sxs-lookup"><span data-stu-id="46150-128">Logging to a file</span></span>

<span data-ttu-id="46150-129">Per la scrittura in un file è necessario creare un <xref:System.IO.StreamWriter> o simile per il file.</span><span class="sxs-lookup"><span data-stu-id="46150-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="46150-130">Il <xref:System.IO.StreamWriter.WriteLine%2A> metodo può quindi essere usato come negli altri esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="46150-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="46150-131">Ricordarsi di assicurarsi che il file venga chiuso senza problemi eliminando il writer quando il contesto viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="46150-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="46150-132">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-132">For example:</span></span>

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
[!code-csharp[LogToFile](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="46150-133">Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per la registrazione in file nelle applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="46150-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="46150-134">Recupero di messaggi dettagliati</span><span class="sxs-lookup"><span data-stu-id="46150-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="46150-135">Dati sensibili</span><span class="sxs-lookup"><span data-stu-id="46150-135">Sensitive data</span></span>

<span data-ttu-id="46150-136">Per impostazione predefinita, EF Core non includerà i valori dei dati nei messaggi di eccezione.</span><span class="sxs-lookup"><span data-stu-id="46150-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="46150-137">Questo è dovuto al fatto che tali dati possono essere riservati e possono essere rivelati nell'uso in produzione se un'eccezione non viene gestita.</span><span class="sxs-lookup"><span data-stu-id="46150-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="46150-138">Tuttavia, la conoscenza dei valori dei dati, in particolare per le chiavi, può essere molto utile durante il debug.</span><span class="sxs-lookup"><span data-stu-id="46150-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="46150-139">Questa operazione può essere abilitata in EF Core chiamando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="46150-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="46150-140">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="46150-141">Eccezioni di query dettagliate</span><span class="sxs-lookup"><span data-stu-id="46150-141">Detailed query exceptions</span></span>

<span data-ttu-id="46150-142">Per motivi di prestazioni, EF Core non esegue il wrapping di ogni chiamata per leggere un valore dal provider di database in un blocco try-catch.</span><span class="sxs-lookup"><span data-stu-id="46150-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="46150-143">Tuttavia, ciò comporta a volte eccezioni difficili da diagnosticare, specialmente quando il database restituisce un valore NULL quando non è consentito dal modello.</span><span class="sxs-lookup"><span data-stu-id="46150-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="46150-144">Se <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> si attiva, EF introdurrà i blocchi try-catch e fornirà quindi errori più dettagliati.</span><span class="sxs-lookup"><span data-stu-id="46150-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="46150-145">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="46150-146">Filtro</span><span class="sxs-lookup"><span data-stu-id="46150-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="46150-147">Livelli di registrazione</span><span class="sxs-lookup"><span data-stu-id="46150-147">Log levels</span></span>

<span data-ttu-id="46150-148">Ogni messaggio del log EF Core viene assegnato a un livello definito dall' <xref:Microsoft.Extensions.Logging.LogLevel> enumerazione.</span><span class="sxs-lookup"><span data-stu-id="46150-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="46150-149">Per impostazione predefinita, EF Core registrazione semplice include tutti i messaggi a `Debug` livello o superiore.</span><span class="sxs-lookup"><span data-stu-id="46150-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="46150-150">`LogTo` è possibile passare un livello minimo superiore per filtrare alcuni messaggi.</span><span class="sxs-lookup"><span data-stu-id="46150-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="46150-151">Ad esempio, passando `Information` i risultati in un set minimo di log limitato all'accesso al database e ad alcuni messaggi di manutenzione.</span><span class="sxs-lookup"><span data-stu-id="46150-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="46150-152">Messaggi specifici</span><span class="sxs-lookup"><span data-stu-id="46150-152">Specific messages</span></span>

<span data-ttu-id="46150-153">A ogni messaggio del log viene assegnato un <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="46150-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="46150-154">È possibile accedere a questi ID dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe o dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> classe per i messaggi specifici relazionali.</span><span class="sxs-lookup"><span data-stu-id="46150-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="46150-155">Un provider di database può anche avere ID specifici del provider in una classe simile.</span><span class="sxs-lookup"><span data-stu-id="46150-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="46150-156">Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="46150-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="46150-157">`LogTo` può essere configurato in modo da registrare solo i messaggi associati a uno o più ID evento.</span><span class="sxs-lookup"><span data-stu-id="46150-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="46150-158">Ad esempio, per registrare solo i messaggi per il contesto inizializzato o eliminato:</span><span class="sxs-lookup"><span data-stu-id="46150-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="46150-159">Categorie di messaggi</span><span class="sxs-lookup"><span data-stu-id="46150-159">Message categories</span></span>

<span data-ttu-id="46150-160">Ogni messaggio di log viene assegnato a una categoria di logger gerarchici denominata.</span><span class="sxs-lookup"><span data-stu-id="46150-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="46150-161">Le categorie sono:</span><span class="sxs-lookup"><span data-stu-id="46150-161">The categories are:</span></span>

| <span data-ttu-id="46150-162">Category</span><span class="sxs-lookup"><span data-stu-id="46150-162">Category</span></span>                                             | <span data-ttu-id="46150-163">Messaggi</span><span class="sxs-lookup"><span data-stu-id="46150-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="46150-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="46150-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="46150-165">Tutti i messaggi di EF Core</span><span class="sxs-lookup"><span data-stu-id="46150-165">All EF Core messages</span></span>
| <span data-ttu-id="46150-166">Microsoft. EntityFrameworkCore. database</span><span class="sxs-lookup"><span data-stu-id="46150-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="46150-167">Tutte le interazioni con i database</span><span class="sxs-lookup"><span data-stu-id="46150-167">All database interactions</span></span>
| <span data-ttu-id="46150-168">Microsoft. EntityFrameworkCore. database. Connection</span><span class="sxs-lookup"><span data-stu-id="46150-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="46150-169">Usi di una connessione di database</span><span class="sxs-lookup"><span data-stu-id="46150-169">Uses of a database connection</span></span>
| <span data-ttu-id="46150-170">Microsoft. EntityFrameworkCore. database. Command</span><span class="sxs-lookup"><span data-stu-id="46150-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="46150-171">Usi di un comando di database</span><span class="sxs-lookup"><span data-stu-id="46150-171">Uses of a database command</span></span>
| <span data-ttu-id="46150-172">Microsoft. EntityFrameworkCore. database. Transaction</span><span class="sxs-lookup"><span data-stu-id="46150-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="46150-173">Utilizzi di una transazione di database</span><span class="sxs-lookup"><span data-stu-id="46150-173">Uses of a database transaction</span></span>
| <span data-ttu-id="46150-174">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="46150-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="46150-175">Salvataggio di entità, escluse le interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="46150-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="46150-176">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="46150-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="46150-177">Tutte le interazioni tra modelli e metadati</span><span class="sxs-lookup"><span data-stu-id="46150-177">All model and metadata interactions</span></span>
| <span data-ttu-id="46150-178">Microsoft. EntityFrameworkCore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="46150-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="46150-179">Convalida modello</span><span class="sxs-lookup"><span data-stu-id="46150-179">Model validation</span></span>
| <span data-ttu-id="46150-180">Microsoft. EntityFrameworkCore. query</span><span class="sxs-lookup"><span data-stu-id="46150-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="46150-181">Query, escluse le interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="46150-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="46150-182">Microsoft. EntityFrameworkCore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="46150-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="46150-183">Eventi generali, ad esempio creazione del contesto</span><span class="sxs-lookup"><span data-stu-id="46150-183">General events, such as context creation</span></span>
| <span data-ttu-id="46150-184">Microsoft. EntityFrameworkCore. impalcatura</span><span class="sxs-lookup"><span data-stu-id="46150-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="46150-185">reverse engineering database</span><span class="sxs-lookup"><span data-stu-id="46150-185">Database reverse engineering</span></span>
| <span data-ttu-id="46150-186">Microsoft. EntityFrameworkCore. Migrations</span><span class="sxs-lookup"><span data-stu-id="46150-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="46150-187">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="46150-187">Migrations</span></span>
| <span data-ttu-id="46150-188">Microsoft. EntityFrameworkCore. rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="46150-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="46150-189">Interazioni rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="46150-189">Change tracking interactions</span></span>

<span data-ttu-id="46150-190">`LogTo` può essere configurato in modo da registrare solo i messaggi da una o più categorie.</span><span class="sxs-lookup"><span data-stu-id="46150-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="46150-191">Ad esempio, per registrare solo le interazioni tra database:</span><span class="sxs-lookup"><span data-stu-id="46150-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="46150-192">Si noti che la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fornisce un'API gerarchica per trovare una categoria ed evita la necessità di utilizzare stringhe hardcoded.</span><span class="sxs-lookup"><span data-stu-id="46150-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="46150-193">Poiché le categorie sono gerarchiche, in questo esempio viene utilizzata la `Database` categoria per includere tutti i messaggi per le sottocategorie `Database.Connection` , `Database.Command` e `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="46150-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="46150-194">Filtri personalizzati</span><span class="sxs-lookup"><span data-stu-id="46150-194">Custom filters</span></span>

<span data-ttu-id="46150-195">`LogTo` consente di usare un filtro personalizzato per i casi in cui nessuna delle opzioni di filtro sopra indicate è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="46150-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="46150-196">Ad esempio, per registrare un messaggio a livello `Information` o superiore, oltre ai messaggi per l'apertura e la chiusura di una connessione:</span><span class="sxs-lookup"><span data-stu-id="46150-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="46150-197">Il filtro usando filtri personalizzati o una delle altre opzioni illustrate qui è più efficiente del filtro nel delegato LogTo.</span><span class="sxs-lookup"><span data-stu-id="46150-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="46150-198">Questo perché se il filtro determina che il messaggio non deve essere registrato, il messaggio del log non viene ancora creato.</span><span class="sxs-lookup"><span data-stu-id="46150-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="message-contents-and-formatting"></a><span data-ttu-id="46150-199">Contenuto e formattazione del messaggio</span><span class="sxs-lookup"><span data-stu-id="46150-199">Message contents and formatting</span></span>

<span data-ttu-id="46150-200">Il contenuto predefinito da `LogTo` viene formattato su più righe.</span><span class="sxs-lookup"><span data-stu-id="46150-200">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="46150-201">La prima riga contiene i metadati del messaggio:</span><span class="sxs-lookup"><span data-stu-id="46150-201">The first line contains message metadata:</span></span>

* <span data-ttu-id="46150-202"><xref:Microsoft.Extensions.Logging.LogLevel>Come prefisso di quattro caratteri</span><span class="sxs-lookup"><span data-stu-id="46150-202">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="46150-203">Timestamp locale, formattato per le impostazioni cultura correnti</span><span class="sxs-lookup"><span data-stu-id="46150-203">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="46150-204">Oggetto <xref:Microsoft.Extensions.Logging.EventId> nel formato che può essere copiato/incollato per ottenere il membro da <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una delle altre `EventId` classi, più il valore ID non elaborato</span><span class="sxs-lookup"><span data-stu-id="46150-204">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="46150-205">Categoria di eventi, come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="46150-205">The event category, as described above.</span></span>

<span data-ttu-id="46150-206">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-206">For example:</span></span>

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

<span data-ttu-id="46150-207">Questo contenuto può essere personalizzato passando i valori da [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="46150-207">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="46150-208">, come illustrato nelle sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="46150-208">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="46150-209">Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per un maggiore controllo sulla formattazione dei log.</span><span class="sxs-lookup"><span data-stu-id="46150-209">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="46150-210">Utilizzo dell'ora UTC</span><span class="sxs-lookup"><span data-stu-id="46150-210">Using UTC time</span></span>

<span data-ttu-id="46150-211">Per impostazione predefinita, timestamnps sono progettati per l'utilizzo locale durante il debug.</span><span class="sxs-lookup"><span data-stu-id="46150-211">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="46150-212">Usare `DbContextLoggerOptions.DefaultWithUtcTime` invece per usare i timestamp UTC indipendenti dalle impostazioni cultura, ma è possibile conservarne tutti gli altri.</span><span class="sxs-lookup"><span data-stu-id="46150-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="46150-213">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-213">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="46150-214">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="46150-214">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="46150-215">Registrazione a riga singola</span><span class="sxs-lookup"><span data-stu-id="46150-215">Single line logging</span></span>

<span data-ttu-id="46150-216">A volte è utile ottenere esattamente una riga per ogni messaggio di log.</span><span class="sxs-lookup"><span data-stu-id="46150-216">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="46150-217">Questa operazione può essere abilitata da `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="46150-217">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="46150-218">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-218">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="46150-219">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="46150-219">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="46150-220">Altre opzioni relative al contenuto</span><span class="sxs-lookup"><span data-stu-id="46150-220">Other content options</span></span>

<span data-ttu-id="46150-221">Altri flag in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="46150-221">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="46150-222">può essere utilizzato per rimuovere la quantità di metadati inclusi nel log.</span><span class="sxs-lookup"><span data-stu-id="46150-222">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="46150-223">Questa operazione può essere utile in combinazione con la registrazione a riga singola.</span><span class="sxs-lookup"><span data-stu-id="46150-223">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="46150-224">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-224">For example:</span></span>

<span data-ttu-id="46150-225">Questo esempio genera la seguente formattazione dei log:</span><span class="sxs-lookup"><span data-stu-id="46150-225">This example results in the following log formatting:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="46150-226">Passaggio da EF6</span><span class="sxs-lookup"><span data-stu-id="46150-226">Moving from EF6</span></span>

<span data-ttu-id="46150-227">EF Core registrazione semplice differisce da <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in due modi importanti:</span><span class="sxs-lookup"><span data-stu-id="46150-227">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="46150-228">I messaggi di log non sono limitati solo alle interazioni tra database</span><span class="sxs-lookup"><span data-stu-id="46150-228">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="46150-229">La registrazione deve essere configurata in fase di inizializzazione del contesto</span><span class="sxs-lookup"><span data-stu-id="46150-229">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="46150-230">Per la prima differenza, è possibile utilizzare il filtro descritto in precedenza per limitare i messaggi che vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="46150-230">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="46150-231">La seconda differenza è una modifica intenzionale che consente di migliorare le prestazioni senza generare messaggi di log quando non sono necessari.</span><span class="sxs-lookup"><span data-stu-id="46150-231">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="46150-232">Tuttavia, è comunque possibile ottenere un comportamento simile a EF6 creando una `Log` Proprietà in `DbContext` e quindi usandola solo quando è stata impostata.</span><span class="sxs-lookup"><span data-stu-id="46150-232">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="46150-233">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46150-233">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
