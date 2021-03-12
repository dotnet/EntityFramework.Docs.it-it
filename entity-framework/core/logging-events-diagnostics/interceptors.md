---
title: Intercettori-EF Core
description: Intercettazione per operazioni di database e altri eventi
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 459c0495e9a2f81e2e84388988f04ca9787080cc
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024225"
---
# <a name="interceptors"></a><span data-ttu-id="ebe88-103">Intercettori</span><span class="sxs-lookup"><span data-stu-id="ebe88-103">Interceptors</span></span>

<span data-ttu-id="ebe88-104">Gli intercettori Entity Framework Core (EF Core) consentono l'intercettazione, la modifica e/o l'eliminazione di EF Core operazioni.</span><span class="sxs-lookup"><span data-stu-id="ebe88-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="ebe88-105">Sono incluse operazioni di database di basso livello, ad esempio l'esecuzione di un comando, nonché operazioni di livello superiore, ad esempio chiamate a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="ebe88-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="ebe88-106">Gli intercettori sono diversi dalla registrazione e dalla diagnostica in quanto consentono la modifica o l'eliminazione dell'operazione intercettata.</span><span class="sxs-lookup"><span data-stu-id="ebe88-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="ebe88-107">La registrazione [semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) è una scelta migliore per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="ebe88-108">Gli intercettori vengono registrati per ogni istanza di DbContext al momento della configurazione del contesto.</span><span class="sxs-lookup"><span data-stu-id="ebe88-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="ebe88-109">Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="ebe88-110">Registrazione degli intercettori</span><span class="sxs-lookup"><span data-stu-id="ebe88-110">Registering interceptors</span></span>

<span data-ttu-id="ebe88-111">Gli intercettori vengono registrati utilizzando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> quando si [configura un'istanza di DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ebe88-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="ebe88-112">Questa operazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ebe88-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ebe88-113">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ebe88-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="ebe88-114">In alternativa, `AddInterceptors` è possibile chiamare come parte di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o quando si crea un' <xref:Microsoft.EntityFrameworkCore.DbContextOptions> istanza da passare al costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="ebe88-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-115">Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring.</span><span class="sxs-lookup"><span data-stu-id="ebe88-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="ebe88-116">Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.</span><span class="sxs-lookup"><span data-stu-id="ebe88-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="ebe88-117">Gli intercettori spesso sono senza stato, il che significa che è possibile usare una singola istanza dell'intercettore per tutte le istanze di DbContext.</span><span class="sxs-lookup"><span data-stu-id="ebe88-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="ebe88-118">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ebe88-118">For example:</span></span>

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

<span data-ttu-id="ebe88-119">Ogni istanza dell'intercettore deve implementare una o più interfacce derivate da <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> .</span><span class="sxs-lookup"><span data-stu-id="ebe88-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="ebe88-120">Ogni istanza deve essere registrata solo una volta, anche se implementa più interfacce di intercettazione. EF Core instraderà gli eventi per ogni interfaccia nel modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="ebe88-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="ebe88-121">Intercettazione database</span><span class="sxs-lookup"><span data-stu-id="ebe88-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="ebe88-122">L'intercettazione del database è stata introdotta in EF Core 3,0 ed è disponibile solo per i provider di database relazionali.</span><span class="sxs-lookup"><span data-stu-id="ebe88-122">Database interception was introduced in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="ebe88-123">Il supporto di salvataggio è stato introdotto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="ebe88-123">Savepoint support was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="ebe88-124">L'intercettazione di database di basso livello è suddivisa nelle tre interfacce illustrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ebe88-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="ebe88-125">Intercettore</span><span class="sxs-lookup"><span data-stu-id="ebe88-125">Interceptor</span></span>                                                            | <span data-ttu-id="ebe88-126">Operazioni di database intercettate</span><span class="sxs-lookup"><span data-stu-id="ebe88-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="ebe88-127">Creazione di comandi</span><span class="sxs-lookup"><span data-stu-id="ebe88-127">Creating commands</span></span></br><span data-ttu-id="ebe88-128">Esecuzione di comandi</span><span class="sxs-lookup"><span data-stu-id="ebe88-128">Executing commands</span></span></br><span data-ttu-id="ebe88-129">Errori di comando</span><span class="sxs-lookup"><span data-stu-id="ebe88-129">Command failures</span></span></br><span data-ttu-id="ebe88-130">Eliminazione della finestra DbDataReader del comando</span><span class="sxs-lookup"><span data-stu-id="ebe88-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="ebe88-131">Apertura e chiusura delle connessioni</span><span class="sxs-lookup"><span data-stu-id="ebe88-131">Opening and closing connections</span></span></br><span data-ttu-id="ebe88-132">Errori di connessione</span><span class="sxs-lookup"><span data-stu-id="ebe88-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="ebe88-133">Creazione di transazioni</span><span class="sxs-lookup"><span data-stu-id="ebe88-133">Creating transactions</span></span></br><span data-ttu-id="ebe88-134">Utilizzo di transazioni esistenti</span><span class="sxs-lookup"><span data-stu-id="ebe88-134">Using existing transactions</span></span></br><span data-ttu-id="ebe88-135">Commit di transazioni</span><span class="sxs-lookup"><span data-stu-id="ebe88-135">Committing transactions</span></span></br><span data-ttu-id="ebe88-136">Rollback di transazioni</span><span class="sxs-lookup"><span data-stu-id="ebe88-136">Rolling back transactions</span></span></br><span data-ttu-id="ebe88-137">Creazione e uso di salvataggio</span><span class="sxs-lookup"><span data-stu-id="ebe88-137">Creating and using savepoints</span></span></br><span data-ttu-id="ebe88-138">Errori delle transazioni</span><span class="sxs-lookup"><span data-stu-id="ebe88-138">Transaction failures</span></span>

<span data-ttu-id="ebe88-139">Le classi base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> e <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contengono implementazioni senza op per ogni metodo nell'interfaccia corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ebe88-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="ebe88-140">Utilizzare le classi base per evitare la necessità di implementare metodi di intercettazione non utilizzati.</span><span class="sxs-lookup"><span data-stu-id="ebe88-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="ebe88-141">I metodi di ogni tipo di intercettore sono in coppia, con la prima chiamata prima che l'operazione di database venga avviata e la seconda dopo il completamento dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="ebe88-142">Ad esempio,</span><span class="sxs-lookup"><span data-stu-id="ebe88-142">For example.</span></span> <span data-ttu-id="ebe88-143">Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> viene chiamato prima dell'esecuzione di una query e <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> viene chiamato dopo che la query è stata inviata al database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="ebe88-144">Ogni coppia di metodi ha sia la sincronizzazione che le variazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="ebe88-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="ebe88-145">Ciò consente l'esecuzione di operazioni di I/O asincrone, ad esempio la richiesta di un token di accesso, come parte dell'intercettazione di un'operazione asincrona sul database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="ebe88-146">Esempio: intercettazione di comandi per aggiungere hint per la query</span><span class="sxs-lookup"><span data-stu-id="ebe88-146">Example: Command interception to add query hints</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-147">È possibile [scaricare l'esempio Command Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CommandInterception) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="ebe88-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="ebe88-148">Un oggetto <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> può essere utilizzato per modificare SQL prima di essere inviato al database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="ebe88-149">Questo esempio illustra come modificare SQL per includere un hint per la query.</span><span class="sxs-lookup"><span data-stu-id="ebe88-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="ebe88-150">Spesso la parte trickiest dell'intercettazione sta determinando quando il comando corrisponde alla query che deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="ebe88-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="ebe88-151">L'analisi di SQL è un'opzione, ma tende a essere fragile.</span><span class="sxs-lookup"><span data-stu-id="ebe88-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="ebe88-152">Un'altra opzione consiste nell'usare [EF Core tag di query](xref:core/querying/tags) per contrassegnare ogni query che deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="ebe88-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="ebe88-153">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ebe88-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="ebe88-154">Questo tag può quindi essere rilevato nell'intercettore perché sarà sempre incluso come commento nella prima riga del testo del comando.</span><span class="sxs-lookup"><span data-stu-id="ebe88-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="ebe88-155">Quando si rileva il tag, la query SQL viene modificata per aggiungere l'hint appropriato:</span><span class="sxs-lookup"><span data-stu-id="ebe88-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

<span data-ttu-id="ebe88-156">Notare:</span><span class="sxs-lookup"><span data-stu-id="ebe88-156">Notice:</span></span>

* <span data-ttu-id="ebe88-157">L'intercettore eredita da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> per evitare di dover implementare ogni metodo nell'interfaccia dell'intercettore.</span><span class="sxs-lookup"><span data-stu-id="ebe88-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="ebe88-158">L'intercettore implementa entrambi i metodi Sync e Async.</span><span class="sxs-lookup"><span data-stu-id="ebe88-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="ebe88-159">In questo modo si garantisce che lo stesso hint per la query venga applicato alle query asincrone e di sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="ebe88-160">L'intercettore implementa i `Executing` metodi che vengono chiamati da EF core con l'oggetto SQL generato _prima_ che venga inviato al database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="ebe88-161">Si confronti con i `Executed` metodi, che vengono chiamati dopo che la chiamata al database ha restituito.</span><span class="sxs-lookup"><span data-stu-id="ebe88-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="ebe88-162">Quando si esegue il tag di una query, l'esecuzione del codice in questo esempio genera quanto segue:</span><span class="sxs-lookup"><span data-stu-id="ebe88-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="ebe88-163">D'altra parte, quando una query non è contrassegnata, viene inviata al database senza modifiche:</span><span class="sxs-lookup"><span data-stu-id="ebe88-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="ebe88-164">Esempio: intercettazione della connessione per l'autenticazione SQL Azure usando Aggiungi</span><span class="sxs-lookup"><span data-stu-id="ebe88-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-165">È possibile [scaricare l'esempio Connection Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/ConnectionInterception) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="ebe88-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="ebe88-166">Un oggetto <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> può essere utilizzato per modificare l'oggetto <xref:System.Data.Common.DbConnection> prima che venga utilizzato per connettersi al database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="ebe88-167">Questa operazione può essere usata per ottenere un token di accesso Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="ebe88-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="ebe88-168">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ebe88-168">For example:</span></span>

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> <span data-ttu-id="ebe88-169">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) supporta ora l'autenticazione AAD tramite la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="ebe88-170">Per altre informazioni, vedere <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod>.</span><span class="sxs-lookup"><span data-stu-id="ebe88-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="ebe88-171">Si noti che l'intercettore genera un'eccezione se viene eseguita una chiamata di sincronizzazione per aprire la connessione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="ebe88-172">Ciò è dovuto al fatto che non è disponibile alcun metodo asincrono per ottenere il token di accesso e non esiste [alcun modo universale e semplice per chiamare un metodo asincrono da un contesto non asincrono senza rischiare il deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span><span class="sxs-lookup"><span data-stu-id="ebe88-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="ebe88-173">in alcune situazioni il token di accesso potrebbe non essere memorizzato automaticamente nella cache del provider di token di Azure.</span><span class="sxs-lookup"><span data-stu-id="ebe88-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="ebe88-174">A seconda del tipo di token richiesto, potrebbe essere necessario implementare la memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="ebe88-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="ebe88-175">Esempio: intercettazione dei comandi avanzati per la memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="ebe88-175">Example: Advanced command interception for caching</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-176">È possibile [scaricare l'esempio Advanced Command Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="ebe88-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="ebe88-177">Gli intercettori di EF Core possono:</span><span class="sxs-lookup"><span data-stu-id="ebe88-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="ebe88-178">Indica EF Core di non visualizzare l'esecuzione dell'operazione intercettata</span><span class="sxs-lookup"><span data-stu-id="ebe88-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="ebe88-179">Modificare il risultato dell'operazione restituito a EF Core</span><span class="sxs-lookup"><span data-stu-id="ebe88-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="ebe88-180">Questo esempio mostra un intercettore che usa queste funzionalità per comportarsi come una cache di secondo livello primitiva.</span><span class="sxs-lookup"><span data-stu-id="ebe88-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="ebe88-181">I risultati della query memorizzati nella cache vengono restituiti per una query specifica, evitando un round trip del database.</span><span class="sxs-lookup"><span data-stu-id="ebe88-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="ebe88-182">Prestare attenzione quando si modifica il comportamento predefinito EF Core in questo modo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="ebe88-183">EF Core possono comportarsi in modo imprevisto se si ottiene un risultato anomalo che non può essere elaborato correttamente.</span><span class="sxs-lookup"><span data-stu-id="ebe88-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="ebe88-184">Inoltre, in questo esempio vengono illustrati i concetti dell'intercettore; non è concepito come modello per un'implementazione affidabile della cache di secondo livello.</span><span class="sxs-lookup"><span data-stu-id="ebe88-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="ebe88-185">In questo esempio, l'applicazione esegue spesso una query per ottenere il messaggio "Daily Message" più recente:</span><span class="sxs-lookup"><span data-stu-id="ebe88-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="ebe88-186">Questa query viene [contrassegnata](xref:core/querying/tags) in modo da poter essere facilmente rilevata nell'intercettore.</span><span class="sxs-lookup"><span data-stu-id="ebe88-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="ebe88-187">L'idea è eseguire solo una query sul database per un nuovo messaggio una volta al giorno.</span><span class="sxs-lookup"><span data-stu-id="ebe88-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="ebe88-188">In altri casi, l'applicazione utilizzerà un risultato memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="ebe88-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="ebe88-189">(Nell'esempio viene utilizzato un ritardo di 10 secondi nell'esempio per simulare un nuovo giorno).</span><span class="sxs-lookup"><span data-stu-id="ebe88-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="ebe88-190">Stato intercettore</span><span class="sxs-lookup"><span data-stu-id="ebe88-190">Interceptor state</span></span>

<span data-ttu-id="ebe88-191">Questo intercettore è con stato: archivia l'ID e il testo del messaggio del messaggio giornaliero più recente sottoposto a query, più l'ora in cui è stata eseguita la query.</span><span class="sxs-lookup"><span data-stu-id="ebe88-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="ebe88-192">A causa di questo stato, è necessario anche un [blocco](/dotnet/csharp/language-reference/keywords/lock-statement) poiché la memorizzazione nella cache richiede che lo stesso intercettore venga utilizzato da più istanze di contesto.</span><span class="sxs-lookup"><span data-stu-id="ebe88-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="ebe88-193">Prima dell'esecuzione</span><span class="sxs-lookup"><span data-stu-id="ebe88-193">Before execution</span></span>

<span data-ttu-id="ebe88-194">Nel `Executing` Metodo (ad esempio prima di effettuare una chiamata al database), l'intercettore rileva la query con tag, quindi controlla se è presente un risultato memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="ebe88-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="ebe88-195">Se viene trovato un risultato di questo tipo, la query viene annullata e vengono invece usati i risultati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="ebe88-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

<span data-ttu-id="ebe88-196">Si noti il modo in cui il codice chiama <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> e passa una sostituzione <xref:System.Data.Common.DbDataReader> contenente i dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="ebe88-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="ebe88-197">Viene quindi restituito questo InterceptionResult, causando l'eliminazione dell'esecuzione della query.</span><span class="sxs-lookup"><span data-stu-id="ebe88-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="ebe88-198">Il lettore di sostituzione viene invece utilizzato da EF Core come risultato della query.</span><span class="sxs-lookup"><span data-stu-id="ebe88-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="ebe88-199">Questo intercettore modifica anche il testo del comando.</span><span class="sxs-lookup"><span data-stu-id="ebe88-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="ebe88-200">Questa manipolazione non è necessaria, ma migliora la chiarezza nei messaggi di log.</span><span class="sxs-lookup"><span data-stu-id="ebe88-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="ebe88-201">Non è necessario che il testo del comando sia SQL valido perché la query non verrà eseguita.</span><span class="sxs-lookup"><span data-stu-id="ebe88-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="ebe88-202">Dopo l'esecuzione</span><span class="sxs-lookup"><span data-stu-id="ebe88-202">After execution</span></span>

<span data-ttu-id="ebe88-203">Se non è disponibile alcun messaggio memorizzato nella cache o se è scaduto, il codice precedente non elimina il risultato.</span><span class="sxs-lookup"><span data-stu-id="ebe88-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="ebe88-204">EF Core eseguirà quindi la query normalmente.</span><span class="sxs-lookup"><span data-stu-id="ebe88-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="ebe88-205">Viene quindi restituito al metodo dell'intercettore `Executed` dopo l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="ebe88-206">A questo punto, se il risultato non è già un reader memorizzato nella cache, il nuovo ID messaggio e la stringa vengono esatti dal lettore reale e memorizzati nella cache pronti per l'uso successivo di questa query.</span><span class="sxs-lookup"><span data-stu-id="ebe88-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a><span data-ttu-id="ebe88-207">Dimostrazione</span><span class="sxs-lookup"><span data-stu-id="ebe88-207">Demonstration</span></span>

<span data-ttu-id="ebe88-208">L' [esempio di intercettore Caching](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) contiene una semplice applicazione console che esegue query per i messaggi giornalieri per testare la memorizzazione nella cache:</span><span class="sxs-lookup"><span data-stu-id="ebe88-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

<span data-ttu-id="ebe88-209">Si ottiene l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="ebe88-209">This results in the following output:</span></span>

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

<span data-ttu-id="ebe88-210">Si noti che dall'output del log l'applicazione continua a usare il messaggio memorizzato nella cache fino alla scadenza del timeout, a quel punto viene eseguita nuovamente una query sul database per eventuali nuovi messaggi.</span><span class="sxs-lookup"><span data-stu-id="ebe88-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="ebe88-211">Intercettazione SaveChanges</span><span class="sxs-lookup"><span data-stu-id="ebe88-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="ebe88-212">L'intercettazione di SaveChanges è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="ebe88-212">SaveChanges interception was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-213">È possibile [scaricare l'esempio di intercettore SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="ebe88-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="ebe88-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>i punti di intercettazione e sono definiti dall' <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interfaccia.</span><span class="sxs-lookup"><span data-stu-id="ebe88-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interface.</span></span> <span data-ttu-id="ebe88-215">Come per gli altri intercettori, la <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> classe base con metodi no-op viene fornita come praticità.</span><span class="sxs-lookup"><span data-stu-id="ebe88-215">As for other interceptors, the <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-216">Gli intercettori sono potenti.</span><span class="sxs-lookup"><span data-stu-id="ebe88-216">Interceptors are powerful.</span></span> <span data-ttu-id="ebe88-217">In molti casi, tuttavia, potrebbe essere più semplice eseguire l'override del metodo SaveChanges o usare gli [eventi .NET per SaveChanges](xref:core/logging-events-diagnostics/events) esposto in DbContext.</span><span class="sxs-lookup"><span data-stu-id="ebe88-217">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="ebe88-218">Esempio: intercettazione SaveChanges per il controllo</span><span class="sxs-lookup"><span data-stu-id="ebe88-218">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="ebe88-219">È possibile intercettare SaveChanges per creare un record di controllo indipendente delle modifiche apportate.</span><span class="sxs-lookup"><span data-stu-id="ebe88-219">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="ebe88-220">Non si tratta di una soluzione di controllo affidabile.</span><span class="sxs-lookup"><span data-stu-id="ebe88-220">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="ebe88-221">Piuttosto, si tratta di un esempio semplicistico utilizzato per illustrare le funzionalità di intercettazione.</span><span class="sxs-lookup"><span data-stu-id="ebe88-221">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="ebe88-222">Contesto dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="ebe88-222">The application context</span></span>

<span data-ttu-id="ebe88-223">L' [esempio per il controllo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) usa un DbContext semplice con Blog e post.</span><span class="sxs-lookup"><span data-stu-id="ebe88-223">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

<span data-ttu-id="ebe88-224">Si noti che una nuova istanza dell'intercettore è registrata per ogni istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="ebe88-224">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="ebe88-225">Questo perché l'intercettore di controllo contiene lo stato collegato all'istanza del contesto corrente.</span><span class="sxs-lookup"><span data-stu-id="ebe88-225">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="ebe88-226">Contesto di controllo</span><span class="sxs-lookup"><span data-stu-id="ebe88-226">The audit context</span></span>

<span data-ttu-id="ebe88-227">L'esempio contiene anche un secondo DbContext e un modello utilizzato per il database di controllo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-227">The sample also contains a second DbContext and model used for the auditing database.</span></span>

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a><span data-ttu-id="ebe88-228">Intercettore</span><span class="sxs-lookup"><span data-stu-id="ebe88-228">The interceptor</span></span>

<span data-ttu-id="ebe88-229">L'idea generale del controllo con l'intercettore è la seguente:</span><span class="sxs-lookup"><span data-stu-id="ebe88-229">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="ebe88-230">Un messaggio di controllo viene creato all'inizio di SaveChanges e viene scritto nel database di controllo</span><span class="sxs-lookup"><span data-stu-id="ebe88-230">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="ebe88-231">SaveChanges può continuare</span><span class="sxs-lookup"><span data-stu-id="ebe88-231">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="ebe88-232">Se SaveChanges ha esito positivo, il messaggio di controllo viene aggiornato per indicare l'esito positivo</span><span class="sxs-lookup"><span data-stu-id="ebe88-232">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="ebe88-233">Se SaveChanges ha esito negativo, il messaggio di controllo viene aggiornato per indicare l'errore</span><span class="sxs-lookup"><span data-stu-id="ebe88-233">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="ebe88-234">La prima fase viene gestita prima che tutte le modifiche vengano inviate al database usando le sostituzioni di <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ebe88-234">The first stage is handled before any changes are sent to the database using overrides of <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType>.</span></span>

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

<span data-ttu-id="ebe88-235">L'override di entrambi i metodi Sync e async garantisce che il controllo avvenga indipendentemente dal fatto che `SaveChanges` `SaveChangesAsync` venga chiamato o.</span><span class="sxs-lookup"><span data-stu-id="ebe88-235">Overriding both sync and async methods ensures that auditing will happen regardless of whether `SaveChanges` or `SaveChangesAsync` are called.</span></span> <span data-ttu-id="ebe88-236">Si noti inoltre che l'overload asincrono è in grado di eseguire operazioni di I/O asincrone non bloccanti nel database di controllo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-236">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="ebe88-237">È possibile che si desideri generare il `SavingChanges` metodo di sincronizzazione per assicurarsi che tutte le operazioni di i/O del database siano asincrone.</span><span class="sxs-lookup"><span data-stu-id="ebe88-237">You may wish to throw from the sync `SavingChanges` method to ensure that all database I/O is async.</span></span> <span data-ttu-id="ebe88-238">Quindi richiede che l'applicazione chiami sempre `SaveChangesAsync` e mai `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="ebe88-238">This then requires that the application always calls `SaveChangesAsync` and never `SaveChanges`.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="ebe88-239">Messaggio di controllo</span><span class="sxs-lookup"><span data-stu-id="ebe88-239">The audit message</span></span>

<span data-ttu-id="ebe88-240">Ogni metodo dell'intercettore dispone di un `eventData` parametro che fornisce informazioni contestuali sull'evento intercettato.</span><span class="sxs-lookup"><span data-stu-id="ebe88-240">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="ebe88-241">In questo caso l'applicazione corrente DbContext viene inclusa nei dati dell'evento, che viene quindi utilizzata per creare un messaggio di controllo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-241">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

<span data-ttu-id="ebe88-242">Il risultato è un' `SaveChangesAudit` entità con una raccolta di `EntityAudit` entità, una per ogni istruzione INSERT, Update o DELETE.</span><span class="sxs-lookup"><span data-stu-id="ebe88-242">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="ebe88-243">L'intercettore inserisce quindi queste entità nel database di controllo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-243">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="ebe88-244">Viene eseguito l'override di ToString in ogni classe di dati di evento EF Core per generare il messaggio di log equivalente per l'evento.</span><span class="sxs-lookup"><span data-stu-id="ebe88-244">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="ebe88-245">Ad esempio, la chiamata di `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inizializzato" BlogsContext "utilizzando il provider" Microsoft. EntityFrameworkCore. sqlite "con options: None".</span><span class="sxs-lookup"><span data-stu-id="ebe88-245">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="ebe88-246">Rilevamento dell'esito positivo</span><span class="sxs-lookup"><span data-stu-id="ebe88-246">Detecting success</span></span>

<span data-ttu-id="ebe88-247">L'entità di controllo è archiviata nell'intercettore, in modo che sia possibile accedervi nuovamente quando SaveChanges ha esito positivo o negativo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-247">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="ebe88-248">Per l'esito positivo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> viene chiamato o.</span><span class="sxs-lookup"><span data-stu-id="ebe88-248">For success, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> is called.</span></span>

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

<span data-ttu-id="ebe88-249">L'entità di controllo è collegata al contesto di controllo, poiché esiste già nel database e deve essere aggiornata.</span><span class="sxs-lookup"><span data-stu-id="ebe88-249">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="ebe88-250">Vengono quindi impostati `Succeeded` e `EndTime` , che contrassegnano queste proprietà come modificate in modo che SaveChanges invii un aggiornamento al database di controllo.</span><span class="sxs-lookup"><span data-stu-id="ebe88-250">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="ebe88-251">Rilevamento di errori</span><span class="sxs-lookup"><span data-stu-id="ebe88-251">Detecting failure</span></span>

<span data-ttu-id="ebe88-252">L'errore viene gestito in modo analogo all'esito positivo, ma <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> nel <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> metodo o.</span><span class="sxs-lookup"><span data-stu-id="ebe88-252">Failure is handled in much the same way as success, but in the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="ebe88-253">I dati dell'evento contengono l'eccezione generata.</span><span class="sxs-lookup"><span data-stu-id="ebe88-253">The event data contains the exception that was thrown.</span></span>

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a><span data-ttu-id="ebe88-254">Dimostrazione</span><span class="sxs-lookup"><span data-stu-id="ebe88-254">Demonstration</span></span>

<span data-ttu-id="ebe88-255">L' [esempio di controllo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) contiene una semplice applicazione console che apporta modifiche al database blogging e quindi Mostra il controllo creato.</span><span class="sxs-lookup"><span data-stu-id="ebe88-255">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
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

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

<span data-ttu-id="ebe88-256">Il risultato Mostra il contenuto del database di controllo:</span><span class="sxs-lookup"><span data-stu-id="ebe88-256">The result shows the contents of the auditing database:</span></span>

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
