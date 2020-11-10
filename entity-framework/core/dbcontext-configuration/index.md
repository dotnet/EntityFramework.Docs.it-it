---
title: Configurazione di un DbContext-EF Core
description: Strategie per la configurazione di oggetti DbContext con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431565"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="ab9cb-103">Configurazione di un DbContext</span><span class="sxs-lookup"><span data-stu-id="ab9cb-103">Configuring a DbContext</span></span>

<span data-ttu-id="ab9cb-104">Questo articolo illustra i modelli di base per la configurazione di un `DbContext` tramite un `DbContextOptions` per connettersi a un database usando uno specifico provider di EF core e comportamenti facoltativi.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="ab9cb-105">Configurazione DbContext della fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="ab9cb-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="ab9cb-106">EF Core strumenti della fase di progettazione, ad esempio le [migrazioni](xref:core/managing-schemas/migrations/index) , devono essere in grado di individuare e creare un'istanza di un `DbContext` tipo funzionante per raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="ab9cb-107">Questo processo può essere automatico purché lo strumento possa creare facilmente il `DbContext` in modo da configurarlo in modo analogo a come verrebbe configurato in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="ab9cb-108">Anche se qualsiasi modello che fornisce le informazioni di configurazione necessarie a `DbContext` può funzionare in fase di esecuzione, gli strumenti che richiedono l'uso di `DbContext` in fase di progettazione possono funzionare solo con un numero limitato di modelli.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="ab9cb-109">Queste informazioni sono descritte più dettagliatamente nella sezione relativa alla [creazione del contesto della fase di progettazione](xref:core/cli/dbcontext-creation) .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="ab9cb-110">Configurazione di DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="ab9cb-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="ab9cb-111">`DbContext` per eseguire qualsiasi operazione, è necessario disporre di un'istanza di `DbContextOptions` .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="ab9cb-112">L' `DbContextOptions` istanza contiene le informazioni di configurazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="ab9cb-113">Provider di database da usare, in genere selezionato richiamando un metodo come `UseSqlServer` o `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="ab9cb-114">Questi metodi di estensione richiedono il pacchetto del provider corrispondente, ad esempio `Microsoft.EntityFrameworkCore.SqlServer` o `Microsoft.EntityFrameworkCore.Sqlite` .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="ab9cb-115">I metodi sono definiti nello `Microsoft.EntityFrameworkCore` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="ab9cb-116">Qualsiasi stringa di connessione o identificatore necessario dell'istanza del database, in genere passato come argomento al metodo di selezione del provider menzionato in precedenza</span><span class="sxs-lookup"><span data-stu-id="ab9cb-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="ab9cb-117">Tutti i selettori di comportamento facoltativi a livello di provider, in genere concatenati all'interno della chiamata al metodo di selezione del provider</span><span class="sxs-lookup"><span data-stu-id="ab9cb-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="ab9cb-118">Tutti i selettori di comportamento di EF Core generali, in genere concatenati dopo o prima del metodo selettore provider</span><span class="sxs-lookup"><span data-stu-id="ab9cb-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="ab9cb-119">Nell'esempio seguente viene configurato `DbContextOptions` per l'utilizzo del provider SQL Server, una connessione contenuta nella `connectionString` variabile, un timeout del comando a livello di provider e un selettore di comportamento EF core che rende tutte le query eseguite in `DbContext` [Nessun rilevamento](xref:core/querying/tracking#no-tracking-queries) per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="ab9cb-120">I metodi del selettore del provider e altri metodi di selezione dei comportamenti indicati in precedenza sono metodi di estensione nelle `DbContextOptions` classi di opzioni specifiche del provider o.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="ab9cb-121">Per poter accedere a questi metodi di estensione, potrebbe essere necessario disporre di uno spazio dei nomi (in genere `Microsoft.EntityFrameworkCore` ) nell'ambito e includere altre dipendenze del pacchetto nel progetto.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="ab9cb-122">`DbContextOptions`È possibile fornire l'oggetto `DbContext` eseguendo l'override del `OnConfiguring` metodo o esternamente tramite un argomento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="ab9cb-123">Se vengono usati entrambi, `OnConfiguring` viene applicato per ultimo e è possibile sovrascrivere le opzioni fornite all'argomento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="ab9cb-124">Argomento del costruttore</span><span class="sxs-lookup"><span data-stu-id="ab9cb-124">Constructor argument</span></span>

<span data-ttu-id="ab9cb-125">Il costruttore può semplicemente accettare un `DbContextOptions` come segue:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> <span data-ttu-id="ab9cb-126">Il costruttore di base di DbContext accetta anche la versione non generica di `DbContextOptions` , ma non è consigliabile usare la versione non generica per le applicazioni con più tipi di contesto.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="ab9cb-127">L'applicazione può ora passare `DbContextOptions` quando si crea un'istanza di un contesto, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="ab9cb-128">Onconfigurazione</span><span class="sxs-lookup"><span data-stu-id="ab9cb-128">OnConfiguring</span></span>

<span data-ttu-id="ab9cb-129">È anche possibile inizializzare l'oggetto `DbContextOptions` all'interno del contesto stesso.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="ab9cb-130">Sebbene sia possibile usare questa tecnica per la configurazione di base, in genere è comunque necessario ottenere determinati dettagli di configurazione dall'esterno, ad esempio una stringa di connessione del database.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="ab9cb-131">Questa operazione può essere eseguita con un'API di configurazione o qualsiasi altro mezzo.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="ab9cb-132">Per eseguire l'inizializzazione `DbContextOptions` all'interno del contesto, eseguire l'override del `OnConfiguring` metodo e chiamare i metodi sull'oggetto fornito `DbContextOptionsBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ab9cb-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="ab9cb-133">Un'applicazione può semplicemente creare un'istanza di tale contesto senza passare alcun elemento al costruttore:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="ab9cb-134">Questo approccio non si presta ai test, a meno che i test non siano destinati al database completo.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="ab9cb-135">Uso di DbContext con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="ab9cb-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="ab9cb-136">EF Core supporta `DbContext` l'utilizzo di con un contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="ab9cb-137">Il tipo di DbContext può essere aggiunto al contenitore del servizio usando il `AddDbContext<TContext>` metodo.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="ab9cb-138">`AddDbContext<TContext>` renderà il tipo di DbContext, `TContext` e l'oggetto corrispondente `DbContextOptions<TContext>` disponibile per l'inserimento dal contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="ab9cb-139">Per ulteriori informazioni sull'inserimento delle dipendenze, vedere [più](#more-reading) avanti.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="ab9cb-140">Aggiunta dell' `DbContext` inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="ab9cb-141">A tale scopo, è necessario aggiungere un [argomento del costruttore](#constructor-argument) al tipo DbContext che accetta `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="ab9cb-142">Codice contesto:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="ab9cb-143">Codice dell'applicazione (in ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="ab9cb-143">Application code (in ASP.NET Core):</span></span>

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="ab9cb-144">Codice dell'applicazione (usando direttamente ServiceProvider, meno comune):</span><span class="sxs-lookup"><span data-stu-id="ab9cb-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="ab9cb-145">Evitare problemi di threading DbContext</span><span class="sxs-lookup"><span data-stu-id="ab9cb-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="ab9cb-146">Entity Framework Core non supporta l'esecuzione di più operazioni parallele nella stessa `DbContext` istanza.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="ab9cb-147">Sono incluse l'esecuzione parallela di query asincrone e qualsiasi utilizzo simultaneo esplicito da più thread.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="ab9cb-148">Pertanto, `await` le chiamate asincrone vengono sempre eseguite immediatamente oppure si utilizzano `DbContext` istanze separate per le operazioni eseguite in parallelo.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="ab9cb-149">Quando EF Core rileva un tentativo di usare un' `DbContext` istanza simultaneamente, verrà visualizzato un `InvalidOperationException` messaggio con un messaggio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="ab9cb-150">Una seconda operazione è stata avviata in questo contesto prima del completamento di un'operazione precedente.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="ab9cb-151">Questo problema è in genere causato da thread diversi che utilizzano la stessa istanza di DbContext, ma non è garantito che i membri di istanza siano thread-safe.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="ab9cb-152">Quando l'accesso simultaneo non viene rilevato, può causare un comportamento indefinito, arresti anomali dell'applicazione e danneggiamento dei dati.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="ab9cb-153">Si verificano errori comuni che possono causare inavvertitamente l'accesso simultaneo nella stessa `DbContext` istanza:</span><span class="sxs-lookup"><span data-stu-id="ab9cb-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="ab9cb-154">Si dimentica di attendere il completamento di un'operazione asincrona prima di avviare qualsiasi altra operazione nello stesso DbContext</span><span class="sxs-lookup"><span data-stu-id="ab9cb-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="ab9cb-155">I metodi asincroni consentono EF Core di avviare operazioni che accedono al database in modo non bloccante.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="ab9cb-156">Tuttavia, se un chiamante non attende il completamento di uno di questi metodi e continua a eseguire altre operazioni su `DbContext` , lo stato di `DbContext` può essere, (e molto probabilmente sarà) danneggiato.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="ab9cb-157">Attendi sempre EF Core metodi asincroni immediatamente.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="ab9cb-158">Condivisione implicita delle istanze di DbContext in più thread tramite l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="ab9cb-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="ab9cb-159">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` Per impostazione predefinita, il metodo di estensione registra i tipi con una [durata con ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="ab9cb-160">Ciò è sicuro dai problemi di accesso simultaneo nella maggior parte delle applicazioni ASP.NET Core perché è presente un solo thread che esegue ogni richiesta client in un determinato momento e perché ogni richiesta ottiene un ambito di inserimento delle dipendenze separato (e quindi un' `DbContext` istanza separata).</span><span class="sxs-lookup"><span data-stu-id="ab9cb-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="ab9cb-161">Per il modello di hosting del server blazer, viene usata una richiesta logica per gestire il circuito utente di blazer e pertanto è disponibile solo un'istanza con ambito DbContext per ogni circuito utente se viene usato l'ambito di inserimento predefinito.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="ab9cb-162">Qualsiasi codice che esegua in modo esplicito più thread in parallelo deve garantire che `DbContext` non venga mai eseguito l'accesso alle istanze simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="ab9cb-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="ab9cb-163">Usando l'inserimento di dipendenze, è possibile ottenere questo risultato registrando il contesto come ambito e creando ambiti (usando `IServiceScopeFactory` ) per ogni thread o registrando `DbContext` come temporaneo (usando l'overload di `AddDbContext` che accetta un `ServiceLifetime` parametro).</span><span class="sxs-lookup"><span data-stu-id="ab9cb-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="ab9cb-164">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="ab9cb-164">More reading</span></span>

- <span data-ttu-id="ab9cb-165">Per altre informazioni sull'uso DI, vedere [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="ab9cb-166">Per ulteriori informazioni, vedere [test](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="ab9cb-166">Read [Testing](xref:core/testing/index) for more information.</span></span>
