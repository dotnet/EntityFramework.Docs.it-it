---
title: Durata, configurazione e inizializzazione di DbContext-EF Core
description: Modelli per la creazione e la gestione di istanze di DbContext con o senza inserimento delle dipendenze
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 5b3143edbcfb82312b8026fb09c96dac85427831
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983521"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="bd441-103">Durata, configurazione e inizializzazione di DbContext</span><span class="sxs-lookup"><span data-stu-id="bd441-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="bd441-104">Questo articolo illustra i modelli di base per l'inizializzazione e la configurazione di un' <xref:Microsoft.EntityFrameworkCore.DbContext> istanza di.</span><span class="sxs-lookup"><span data-stu-id="bd441-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="bd441-105">Durata DbContext</span><span class="sxs-lookup"><span data-stu-id="bd441-105">The DbContext lifetime</span></span>

<span data-ttu-id="bd441-106">Il ciclo di vita di un oggetto `DbContext` inizia al momento della creazione dell'istanza e termina quando l'istanza viene [eliminata](/dotnet/standard/garbage-collection/unmanaged).</span><span class="sxs-lookup"><span data-stu-id="bd441-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="bd441-107">Un' `DbContext` istanza è progettata per essere utilizzata per una _singola_ [unità di lavoro](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="bd441-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="bd441-108">Ciò significa che la durata di un' `DbContext` istanza è in genere molto breve.</span><span class="sxs-lookup"><span data-stu-id="bd441-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="bd441-109">Per citare Martin Fowler dal collegamento precedente, "un'unità di lavoro tiene traccia di tutte le operazioni eseguite durante una transazione aziendale che può influire sul database.</span><span class="sxs-lookup"><span data-stu-id="bd441-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="bd441-110">Al termine, vengono individuate tutte le operazioni che è necessario eseguire per modificare il database come risultato del lavoro ".</span><span class="sxs-lookup"><span data-stu-id="bd441-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="bd441-111">Un'unità di lavoro tipica quando si usa Entity Framework Core (EF Core) implica:</span><span class="sxs-lookup"><span data-stu-id="bd441-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="bd441-112">Creazione di un' `DbContext` istanza</span><span class="sxs-lookup"><span data-stu-id="bd441-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="bd441-113">Rilevamento delle istanze di entità in base al contesto.</span><span class="sxs-lookup"><span data-stu-id="bd441-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="bd441-114">Le entità vengono rilevate da</span><span class="sxs-lookup"><span data-stu-id="bd441-114">Entities become tracked by</span></span>
  - <span data-ttu-id="bd441-115">[Restituito da una query](xref:core/querying/tracking)</span><span class="sxs-lookup"><span data-stu-id="bd441-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="bd441-116">[Aggiunto o collegato al contesto](xref:core/saving/disconnected-entities)</span><span class="sxs-lookup"><span data-stu-id="bd441-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="bd441-117">Le modifiche vengono apportate alle entità rilevate in base alle esigenze per implementare la regola di business</span><span class="sxs-lookup"><span data-stu-id="bd441-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="bd441-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>viene chiamato o.</span><span class="sxs-lookup"><span data-stu-id="bd441-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="bd441-119">EF Core rileva le modifiche apportate e le scrive nel database.</span><span class="sxs-lookup"><span data-stu-id="bd441-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="bd441-120">L' `DbContext` istanza è stata eliminata</span><span class="sxs-lookup"><span data-stu-id="bd441-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="bd441-121">È molto importante eliminare il dopo l' <xref:Microsoft.EntityFrameworkCore.DbContext> utilizzo.</span><span class="sxs-lookup"><span data-stu-id="bd441-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="bd441-122">In questo modo si garantisce che tutte le risorse non gestite vengano liberate e che venga annullata la registrazione di tutti gli eventi o altri hook per evitare perdite di memoria nel caso in cui l'istanza rimanga a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="bd441-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="bd441-123">[DbContext **non è thread-safe**](#avoiding-dbcontext-threading-issues).</span><span class="sxs-lookup"><span data-stu-id="bd441-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="bd441-124">Non condividere contesti tra thread.</span><span class="sxs-lookup"><span data-stu-id="bd441-124">Do not share contexts between threads.</span></span> <span data-ttu-id="bd441-125">Assicurarsi di [attendere](/dotnet/csharp/language-reference/operators/await) tutte le chiamate asincrone prima di continuare a usare l'istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="bd441-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="bd441-126">Un oggetto <xref:System.InvalidOperationException> generato da EF Core codice può impostare lo stato irreversibile per il contesto.</span><span class="sxs-lookup"><span data-stu-id="bd441-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="bd441-127">Tali eccezioni indicano un errore di programma e non sono progettate per il ripristino da.</span><span class="sxs-lookup"><span data-stu-id="bd441-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="bd441-128">DbContext nell'inserimento delle dipendenze per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd441-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="bd441-129">In molte applicazioni Web ciascuna richiesta HTTP corrisponde a una singola unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="bd441-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="bd441-130">In questo modo, la durata del contesto è vincolata a quella della richiesta, un valore predefinito valido per le applicazioni Web.</span><span class="sxs-lookup"><span data-stu-id="bd441-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="bd441-131">ASP.NET Core le applicazioni vengono [configurate tramite l'inserimento di dipendenze](/aspnet/core/fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="bd441-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="bd441-132">È possibile aggiungere EF Core a questa configurazione utilizzando <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> nel [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) metodo di `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="bd441-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="bd441-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="bd441-134">In questo esempio viene registrata una `DbContext` sottoclasse denominata `ApplicationDbContext` come un servizio con ambito nel provider di servizi dell'applicazione ASP.NET Core (noto anche come</span><span class="sxs-lookup"><span data-stu-id="bd441-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="bd441-135">contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bd441-135">the dependency injection container).</span></span> <span data-ttu-id="bd441-136">Il contesto è configurato per l'utilizzo del provider di database SQL Server e leggerà la stringa di connessione da ASP.NET Core configurazione.</span><span class="sxs-lookup"><span data-stu-id="bd441-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="bd441-137">In genere non è rilevante _dove_ `ConfigureServices` viene effettuata la chiamata a `AddDbContext` .</span><span class="sxs-lookup"><span data-stu-id="bd441-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="bd441-138">La `ApplicationDbContext` classe deve esporre un costruttore pubblico con un `DbContextOptions<ApplicationDbContext>` parametro.</span><span class="sxs-lookup"><span data-stu-id="bd441-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="bd441-139">Questo è il modo in cui la configurazione del contesto da `AddDbContext` viene passata a `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="bd441-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="bd441-140">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-141">`ApplicationDbContext` può quindi essere utilizzato in ASP.NET Core controller o altri servizi tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="bd441-142">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="bd441-143">Il risultato finale è un' `ApplicationDbContext` istanza creata per ogni richiesta e passata al controller per eseguire un'unità di lavoro prima di essere eliminata al termine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bd441-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="bd441-144">Leggere più avanti in questo articolo per altre informazioni sulle opzioni di configurazione.</span><span class="sxs-lookup"><span data-stu-id="bd441-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="bd441-145">Vedere anche avvio dell' [app in ASP.NET Core](/aspnet/core/fundamentals/startup) e [inserimento delle dipendenze in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) per altre informazioni sulla configurazione e l'inserimento delle dipendenze in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bd441-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="bd441-146">Inizializzazione DbContext semplice con ' New '</span><span class="sxs-lookup"><span data-stu-id="bd441-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="bd441-147">`DbContext` le istanze possono essere costruite nel normale modo .NET, ad esempio con `new` in C#.</span><span class="sxs-lookup"><span data-stu-id="bd441-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="bd441-148">La configurazione può essere eseguita eseguendo l'override del `OnConfiguring` metodo oppure passando opzioni al costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="bd441-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-150">Questo modello consente inoltre di passare facilmente la configurazione come la stringa di connessione tramite il `DbContext` costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="bd441-151">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-152">In alternativa, `DbContextOptionsBuilder` è possibile usare per creare un `DbContextOptions` oggetto che viene quindi passato al `DbContext` costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="bd441-153">`DbContext`In questo modo è possibile costruire in modo esplicito anche un oggetto configurato per l'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bd441-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="bd441-154">Ad esempio, quando si usa `ApplicationDbContext` defined per ASP.NET Core app Web precedenti:</span><span class="sxs-lookup"><span data-stu-id="bd441-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-155">`DbContextOptions`È possibile creare e il costruttore può essere chiamato in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="bd441-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="bd441-156">Uso di una factory DbContext (ad esempio, per blazer)</span><span class="sxs-lookup"><span data-stu-id="bd441-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="bd441-157">Alcuni tipi di applicazioni, ad esempio [ASP.NET Core Blazer](/aspnet/core/blazor/), utilizzano l'inserimento di dipendenze, ma non creano un ambito del servizio che si allinea alla `DbContext` durata desiderata.</span><span class="sxs-lookup"><span data-stu-id="bd441-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="bd441-158">Anche se tale allineamento esiste, è possibile che l'applicazione debba eseguire più unità di lavoro all'interno di questo ambito.</span><span class="sxs-lookup"><span data-stu-id="bd441-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="bd441-159">Ad esempio, più unità di lavoro all'interno di un'unica richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="bd441-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="bd441-160">In questi casi, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> è possibile usare per registrare una factory per la creazione di `DbContext` istanze.</span><span class="sxs-lookup"><span data-stu-id="bd441-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="bd441-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="bd441-162">La `ApplicationDbContext` classe deve esporre un costruttore pubblico con un `DbContextOptions<ApplicationDbContext>` parametro.</span><span class="sxs-lookup"><span data-stu-id="bd441-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="bd441-163">Si tratta dello stesso modello usato nella sezione tradizionale ASP.NET Core precedente.</span><span class="sxs-lookup"><span data-stu-id="bd441-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-164">La `DbContextFactory` Factory può quindi essere usata in altri servizi tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="bd441-165">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="bd441-166">La Factory inserita può quindi essere usata per costruire istanze di DbContext nel codice del servizio.</span><span class="sxs-lookup"><span data-stu-id="bd441-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="bd441-167">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="bd441-168">Si noti che le `DbContext` istanze create in questo modo **non** sono gestite dal provider di servizi dell'applicazione e pertanto devono essere eliminate dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="bd441-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="bd441-169">Per altre informazioni sull'uso di EF Core con blazer, vedere [ASP.NET Core Server Blazer con Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) .</span><span class="sxs-lookup"><span data-stu-id="bd441-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="bd441-170">DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="bd441-170">DbContextOptions</span></span>

<span data-ttu-id="bd441-171">Il punto di partenza per tutte le `DbContext` configurazioni è <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="bd441-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="bd441-172">Sono disponibili tre modi per ottenere questo generatore:</span><span class="sxs-lookup"><span data-stu-id="bd441-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="bd441-173">In `AddDbContext` e metodi correlati</span><span class="sxs-lookup"><span data-stu-id="bd441-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="bd441-174">In `OnConfiguring`</span><span class="sxs-lookup"><span data-stu-id="bd441-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="bd441-175">Costruito in modo esplicito con `new`</span><span class="sxs-lookup"><span data-stu-id="bd441-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="bd441-176">Gli esempi di ognuno di questi elementi sono illustrati nelle sezioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="bd441-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="bd441-177">È possibile applicare la stessa configurazione indipendentemente dalla provenienza del generatore.</span><span class="sxs-lookup"><span data-stu-id="bd441-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="bd441-178">Inoltre, `OnConfiguring` viene sempre chiamato indipendentemente dalla modalità di costruzione del contesto.</span><span class="sxs-lookup"><span data-stu-id="bd441-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="bd441-179">Questo significa `OnConfiguring` che può essere usato per eseguire una configurazione aggiuntiva anche quando `AddDbContext` viene usato.</span><span class="sxs-lookup"><span data-stu-id="bd441-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="bd441-180">Configurazione del provider di database</span><span class="sxs-lookup"><span data-stu-id="bd441-180">Configuring the database provider</span></span>

<span data-ttu-id="bd441-181">Ogni `DbContext` istanza deve essere configurata per l'utilizzo di un solo provider di database.</span><span class="sxs-lookup"><span data-stu-id="bd441-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="bd441-182">(Le diverse istanze di un `DbContext` sottotipo possono essere utilizzate con provider di database diversi, ma una sola istanza deve utilizzarne solo una). Un provider di database viene configurato utilizzando una specifica `Use*` chiamata ".</span><span class="sxs-lookup"><span data-stu-id="bd441-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="bd441-183">Ad esempio, per utilizzare il provider di database SQL Server:</span><span class="sxs-lookup"><span data-stu-id="bd441-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-184">Questi `Use*` "metodi sono metodi di estensione implementati dal provider di database.</span><span class="sxs-lookup"><span data-stu-id="bd441-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="bd441-185">Questo significa che il pacchetto NuGet del provider di database deve essere installato prima di poter usare il metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="bd441-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="bd441-186">I provider di database EF Core utilizzano in modo estensivo i [metodi di estensione](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span><span class="sxs-lookup"><span data-stu-id="bd441-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="bd441-187">Se il compilatore indica che non è possibile trovare un metodo, verificare che il pacchetto NuGet del provider sia installato e che sia presente `using Microsoft.EntityFrameworkCore;` nel codice.</span><span class="sxs-lookup"><span data-stu-id="bd441-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="bd441-188">La tabella seguente contiene esempi per i provider di database comuni.</span><span class="sxs-lookup"><span data-stu-id="bd441-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="bd441-189">Sistema di database</span><span class="sxs-lookup"><span data-stu-id="bd441-189">Database system</span></span>              | <span data-ttu-id="bd441-190">Configurazione di esempio</span><span class="sxs-lookup"><span data-stu-id="bd441-190">Example configuration</span></span>                         | <span data-ttu-id="bd441-191">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="bd441-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="bd441-192">SQL Server o Azure SQL</span><span class="sxs-lookup"><span data-stu-id="bd441-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="bd441-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="bd441-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="bd441-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="bd441-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="bd441-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="bd441-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="bd441-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="bd441-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="bd441-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="bd441-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="bd441-198">Database in memoria EF Core</span><span class="sxs-lookup"><span data-stu-id="bd441-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="bd441-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="bd441-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="bd441-200">PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="bd441-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="bd441-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="bd441-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="bd441-202">MySQL/MariaDB \*</span><span class="sxs-lookup"><span data-stu-id="bd441-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="bd441-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="bd441-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="bd441-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="bd441-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="bd441-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="bd441-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="bd441-206">\* Questi provider di database non sono forniti da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bd441-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="bd441-207">Per ulteriori informazioni sui provider di database, vedere [provider di database](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="bd441-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="bd441-208">Il EF Core database in memoria non è progettato per l'uso in produzione.</span><span class="sxs-lookup"><span data-stu-id="bd441-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="bd441-209">Inoltre, potrebbe non essere la scelta migliore anche per i test.</span><span class="sxs-lookup"><span data-stu-id="bd441-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="bd441-210">Per ulteriori informazioni, vedere [test del codice che usa EF Core](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="bd441-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="bd441-211">Per ulteriori informazioni sull'utilizzo delle stringhe di connessione con EF Core, vedere [stringhe di connessione](xref:core/miscellaneous/connection-strings) .</span><span class="sxs-lookup"><span data-stu-id="bd441-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="bd441-212">La configurazione facoltativa specifica del provider di database viene eseguita in un generatore aggiuntivo specifico del provider.</span><span class="sxs-lookup"><span data-stu-id="bd441-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="bd441-213">Ad esempio, usando <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> per configurare i tentativi di resilienza della connessione quando ci si connette a SQL di Azure:</span><span class="sxs-lookup"><span data-stu-id="bd441-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="bd441-214">Lo stesso provider di database viene usato per SQL Server e Azure SQL.</span><span class="sxs-lookup"><span data-stu-id="bd441-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="bd441-215">È tuttavia consigliabile utilizzare la [resilienza della connessione](xref:core/miscellaneous/connection-resiliency) per la connessione a SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="bd441-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="bd441-216">Per ulteriori informazioni sulla configurazione specifica del provider, vedere [provider di database](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="bd441-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="bd441-217">Altra configurazione di DbContext</span><span class="sxs-lookup"><span data-stu-id="bd441-217">Other DbContext configuration</span></span>

<span data-ttu-id="bd441-218">`DbContext`È possibile concatenare un'altra configurazione prima o dopo (non fa alcuna differenza) la `Use*` chiamata.</span><span class="sxs-lookup"><span data-stu-id="bd441-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="bd441-219">Ad esempio, per attivare la registrazione dei dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="bd441-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="bd441-220">La tabella seguente contiene esempi di metodi comuni chiamati su `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="bd441-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="bd441-221">Metodo DbContextOptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="bd441-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="bd441-222">Funzione</span><span class="sxs-lookup"><span data-stu-id="bd441-222">What it does</span></span>                                                | <span data-ttu-id="bd441-223">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="bd441-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="bd441-224">Imposta il comportamento di rilevamento predefinito per le query</span><span class="sxs-lookup"><span data-stu-id="bd441-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="bd441-225">Comportamento di rilevamento delle query</span><span class="sxs-lookup"><span data-stu-id="bd441-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="bd441-226">Un modo semplice per ottenere i log di EF Core (EF Core 5,0 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="bd441-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="bd441-227">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="bd441-228">Registra una `Microsoft.Extensions.Logging` Factory</span><span class="sxs-lookup"><span data-stu-id="bd441-228">Registers an `Microsoft.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="bd441-229">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="bd441-230">Include i dati dell'applicazione in eccezioni e registrazione</span><span class="sxs-lookup"><span data-stu-id="bd441-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="bd441-231">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="bd441-232">Errori di query più dettagliati (a scapito delle prestazioni)</span><span class="sxs-lookup"><span data-stu-id="bd441-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="bd441-233">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="bd441-234">Ignora o genera per avvisi e altri eventi</span><span class="sxs-lookup"><span data-stu-id="bd441-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="bd441-235">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="bd441-236">Registra EF Core intercettori</span><span class="sxs-lookup"><span data-stu-id="bd441-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="bd441-237">Registrazione, eventi e diagnostica</span><span class="sxs-lookup"><span data-stu-id="bd441-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="bd441-238">Usare proxy dinamici per il caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="bd441-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="bd441-239">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="bd441-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="bd441-240">Usare proxy dinamici per il rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="bd441-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="bd441-241">Presto disponibile...</span><span class="sxs-lookup"><span data-stu-id="bd441-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="bd441-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> e <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sono metodi di estensione dal pacchetto NuGet [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="bd441-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="bd441-243">Questo tipo di ". La chiamata a UseSomething () è la modalità consigliata per configurare e/o utilizzare EF Core estensioni contenute in altri pacchetti.</span><span class="sxs-lookup"><span data-stu-id="bd441-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-versus-dbcontextoptionstcontext"></a><span data-ttu-id="bd441-244">`DbContextOptions` contro `DbContextOptions<TContext>`</span><span class="sxs-lookup"><span data-stu-id="bd441-244">`DbContextOptions` versus `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="bd441-245">La maggior parte delle `DbContext` sottoclassi che accettano un oggetto `DbContextOptions` deve usare la variazione [generica](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="bd441-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="bd441-246">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="bd441-247">Ciò garantisce che le opzioni corrette per il `DbContext` sottotipo specifico vengano risolte dall'inserimento di dipendenze, anche quando `DbContext` vengono registrati più sottotipi.</span><span class="sxs-lookup"><span data-stu-id="bd441-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="bd441-248">Il DbContext non deve essere sealed, ma la procedura consigliata per le classi non è progettata per essere ereditata da.</span><span class="sxs-lookup"><span data-stu-id="bd441-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="bd441-249">Tuttavia, se il `DbContext` sottotipo è destinato a essere ereditato da, deve esporre un costruttore protetto che accetta un oggetto non generico `DbContextOptions` .</span><span class="sxs-lookup"><span data-stu-id="bd441-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="bd441-250">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="bd441-251">In questo modo, più sottoclassi concrete possono chiamare il costruttore di base usando le diverse istanze generiche `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="bd441-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="bd441-252">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="bd441-253">Si noti che questo è esattamente lo stesso modello che si verifica quando si eredita `DbContext` direttamente da.</span><span class="sxs-lookup"><span data-stu-id="bd441-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="bd441-254">Ovvero, `DbContext` per questo motivo il costruttore stesso accetta un oggetto non generico `DbContextOptions` .</span><span class="sxs-lookup"><span data-stu-id="bd441-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="bd441-255">Una `DbContext` sottoclasse progettata per essere creata ed ereditata da deve esporre entrambe le forme del costruttore.</span><span class="sxs-lookup"><span data-stu-id="bd441-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="bd441-256">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bd441-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="bd441-257">Configurazione DbContext della fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="bd441-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="bd441-258">EF Core strumenti della fase di progettazione, ad esempio quelli per le [migrazioni di EF Core](xref:core/managing-schemas/migrations/index) , devono essere in grado di individuare e creare un'istanza di un tipo funzionante per `DbContext` raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database.</span><span class="sxs-lookup"><span data-stu-id="bd441-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="bd441-259">Questo processo può essere automatico purché lo strumento possa creare facilmente il `DbContext` in modo da configurarlo in modo analogo a come verrebbe configurato in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="bd441-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="bd441-260">Anche se qualsiasi modello che fornisce le informazioni di configurazione necessarie a `DbContext` può funzionare in fase di esecuzione, gli strumenti che richiedono l'uso di `DbContext` in fase di progettazione possono funzionare solo con un numero limitato di modelli.</span><span class="sxs-lookup"><span data-stu-id="bd441-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="bd441-261">Questi sono trattati in modo più dettagliato nella [creazione del contesto della fase di progettazione](xref:core/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="bd441-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="bd441-262">Evitare problemi di threading DbContext</span><span class="sxs-lookup"><span data-stu-id="bd441-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="bd441-263">Entity Framework Core non supporta l'esecuzione di più operazioni parallele nella stessa `DbContext` istanza.</span><span class="sxs-lookup"><span data-stu-id="bd441-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="bd441-264">Sono incluse l'esecuzione parallela di query asincrone e qualsiasi utilizzo simultaneo esplicito da più thread.</span><span class="sxs-lookup"><span data-stu-id="bd441-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="bd441-265">Pertanto, `await` le chiamate asincrone vengono sempre eseguite immediatamente oppure si utilizzano `DbContext` istanze separate per le operazioni eseguite in parallelo.</span><span class="sxs-lookup"><span data-stu-id="bd441-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="bd441-266">Quando EF Core rileva un tentativo di usare un' `DbContext` istanza simultaneamente, verrà visualizzato un `InvalidOperationException` messaggio con un messaggio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="bd441-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="bd441-267">Una seconda operazione è stata avviata in questo contesto prima del completamento di un'operazione precedente.</span><span class="sxs-lookup"><span data-stu-id="bd441-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="bd441-268">Questo problema è in genere causato da thread diversi che utilizzano la stessa istanza di DbContext, ma non è garantito che i membri di istanza siano thread-safe.</span><span class="sxs-lookup"><span data-stu-id="bd441-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="bd441-269">Quando l'accesso simultaneo non viene rilevato, può causare un comportamento indefinito, arresti anomali dell'applicazione e danneggiamento dei dati.</span><span class="sxs-lookup"><span data-stu-id="bd441-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="bd441-270">Si verificano errori comuni che possono causare inavvertitamente l'accesso simultaneo nella stessa `DbContext` istanza:</span><span class="sxs-lookup"><span data-stu-id="bd441-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="bd441-271">Problemi di operazione asincrona</span><span class="sxs-lookup"><span data-stu-id="bd441-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="bd441-272">I metodi asincroni consentono EF Core di avviare operazioni che accedono al database in modo non bloccante.</span><span class="sxs-lookup"><span data-stu-id="bd441-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="bd441-273">Tuttavia, se un chiamante non attende il completamento di uno di questi metodi e continua a eseguire altre operazioni su `DbContext` , lo stato di `DbContext` può essere, (e molto probabilmente sarà) danneggiato.</span><span class="sxs-lookup"><span data-stu-id="bd441-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="bd441-274">Attendi sempre EF Core metodi asincroni immediatamente.</span><span class="sxs-lookup"><span data-stu-id="bd441-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="bd441-275">Condivisione implicita delle istanze di DbContext tramite l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="bd441-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="bd441-276">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` Per impostazione predefinita, il metodo di estensione registra i tipi con una [durata con ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="bd441-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="bd441-277">Ciò è sicuro dai problemi di accesso simultaneo nella maggior parte delle applicazioni ASP.NET Core perché è presente un solo thread che esegue ogni richiesta client in un determinato momento e perché ogni richiesta ottiene un ambito di inserimento delle dipendenze separato (e quindi un' `DbContext` istanza separata).</span><span class="sxs-lookup"><span data-stu-id="bd441-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="bd441-278">Per il modello di hosting del server blazer, viene usata una richiesta logica per gestire il circuito utente di blazer e pertanto è disponibile solo un'istanza con ambito DbContext per ogni circuito utente se viene usato l'ambito di inserimento predefinito.</span><span class="sxs-lookup"><span data-stu-id="bd441-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="bd441-279">Qualsiasi codice che esegua in modo esplicito più thread in parallelo deve garantire che `DbContext` non venga mai eseguito l'accesso alle istanze simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="bd441-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="bd441-280">Usando l'inserimento di dipendenze, è possibile ottenere questo risultato registrando il contesto come ambito e creando ambiti (usando `IServiceScopeFactory` ) per ogni thread o registrando `DbContext` come temporaneo (usando l'overload di `AddDbContext` che accetta un `ServiceLifetime` parametro).</span><span class="sxs-lookup"><span data-stu-id="bd441-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="bd441-281">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="bd441-281">More reading</span></span>

- <span data-ttu-id="bd441-282">Per altre informazioni sull'uso DI, vedere [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="bd441-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="bd441-283">Per ulteriori informazioni, vedere [test](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="bd441-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
