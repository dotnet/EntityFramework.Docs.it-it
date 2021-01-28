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
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>Durata, configurazione e inizializzazione di DbContext

Questo articolo illustra i modelli di base per l'inizializzazione e la configurazione di un' <xref:Microsoft.EntityFrameworkCore.DbContext> istanza di.

## <a name="the-dbcontext-lifetime"></a>Durata DbContext

Il ciclo di vita di un oggetto `DbContext` inizia al momento della creazione dell'istanza e termina quando l'istanza viene [eliminata](/dotnet/standard/garbage-collection/unmanaged). Un' `DbContext` istanza è progettata per essere utilizzata per una _singola_ [unità di lavoro](https://www.martinfowler.com/eaaCatalog/unitOfWork.html). Ciò significa che la durata di un' `DbContext` istanza è in genere molto breve.

> [!TIP]
> Per citare Martin Fowler dal collegamento precedente, "un'unità di lavoro tiene traccia di tutte le operazioni eseguite durante una transazione aziendale che può influire sul database. Al termine, vengono individuate tutte le operazioni che è necessario eseguire per modificare il database come risultato del lavoro ".

Un'unità di lavoro tipica quando si usa Entity Framework Core (EF Core) implica:

- Creazione di un' `DbContext` istanza
- Rilevamento delle istanze di entità in base al contesto. Le entità vengono rilevate da
  - [Restituito da una query](xref:core/querying/tracking)
  - [Aggiunto o collegato al contesto](xref:core/saving/disconnected-entities)
- Le modifiche vengono apportate alle entità rilevate in base alle esigenze per implementare la regola di business
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>viene chiamato o. EF Core rileva le modifiche apportate e le scrive nel database.
- L' `DbContext` istanza è stata eliminata

> [!IMPORTANT]
>
> - È molto importante eliminare il dopo l' <xref:Microsoft.EntityFrameworkCore.DbContext> utilizzo. In questo modo si garantisce che tutte le risorse non gestite vengano liberate e che venga annullata la registrazione di tutti gli eventi o altri hook per evitare perdite di memoria nel caso in cui l'istanza rimanga a cui si fa riferimento.
> - [DbContext **non è thread-safe**](#avoiding-dbcontext-threading-issues). Non condividere contesti tra thread. Assicurarsi di [attendere](/dotnet/csharp/language-reference/operators/await) tutte le chiamate asincrone prima di continuare a usare l'istanza di contesto.
> - Un oggetto <xref:System.InvalidOperationException> generato da EF Core codice può impostare lo stato irreversibile per il contesto. Tali eccezioni indicano un errore di programma e non sono progettate per il ripristino da.

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>DbContext nell'inserimento delle dipendenze per ASP.NET Core

In molte applicazioni Web ciascuna richiesta HTTP corrisponde a una singola unità di lavoro. In questo modo, la durata del contesto è vincolata a quella della richiesta, un valore predefinito valido per le applicazioni Web.

ASP.NET Core le applicazioni vengono [configurate tramite l'inserimento di dipendenze](/aspnet/core/fundamentals/startup). È possibile aggiungere EF Core a questa configurazione utilizzando <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> nel [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) metodo di `Startup.cs` . Ad esempio:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

In questo esempio viene registrata una `DbContext` sottoclasse denominata `ApplicationDbContext` come un servizio con ambito nel provider di servizi dell'applicazione ASP.NET Core (noto anche come contenitore di inserimento delle dipendenze. Il contesto è configurato per l'utilizzo del provider di database SQL Server e leggerà la stringa di connessione da ASP.NET Core configurazione. In genere non è rilevante _dove_ `ConfigureServices` viene effettuata la chiamata a `AddDbContext` .

La `ApplicationDbContext` classe deve esporre un costruttore pubblico con un `DbContextOptions<ApplicationDbContext>` parametro. Questo è il modo in cui la configurazione del contesto da `AddDbContext` viene passata a `DbContext` . Ad esempio:

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

`ApplicationDbContext` può quindi essere utilizzato in ASP.NET Core controller o altri servizi tramite l'inserimento del costruttore. Ad esempio:

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

Il risultato finale è un' `ApplicationDbContext` istanza creata per ogni richiesta e passata al controller per eseguire un'unità di lavoro prima di essere eliminata al termine della richiesta.

Leggere più avanti in questo articolo per altre informazioni sulle opzioni di configurazione. Vedere anche avvio dell' [app in ASP.NET Core](/aspnet/core/fundamentals/startup) e [inserimento delle dipendenze in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) per altre informazioni sulla configurazione e l'inserimento delle dipendenze in ASP.NET Core.

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>Inizializzazione DbContext semplice con ' New '

`DbContext` le istanze possono essere costruite nel normale modo .NET, ad esempio con `new` in C#. La configurazione può essere eseguita eseguendo l'override del `OnConfiguring` metodo oppure passando opzioni al costruttore. Ad esempio:

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

Questo modello consente inoltre di passare facilmente la configurazione come la stringa di connessione tramite il `DbContext` costruttore. Ad esempio:

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

In alternativa, `DbContextOptionsBuilder` è possibile usare per creare un `DbContextOptions` oggetto che viene quindi passato al `DbContext` costruttore. `DbContext`In questo modo è possibile costruire in modo esplicito anche un oggetto configurato per l'inserimento di dipendenze. Ad esempio, quando si usa `ApplicationDbContext` defined per ASP.NET Core app Web precedenti:

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

`DbContextOptions`È possibile creare e il costruttore può essere chiamato in modo esplicito:

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>Uso di una factory DbContext (ad esempio, per blazer)

Alcuni tipi di applicazioni, ad esempio [ASP.NET Core Blazer](/aspnet/core/blazor/), utilizzano l'inserimento di dipendenze, ma non creano un ambito del servizio che si allinea alla `DbContext` durata desiderata. Anche se tale allineamento esiste, è possibile che l'applicazione debba eseguire più unità di lavoro all'interno di questo ambito. Ad esempio, più unità di lavoro all'interno di un'unica richiesta HTTP.

In questi casi, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> è possibile usare per registrare una factory per la creazione di `DbContext` istanze. Ad esempio:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

La `ApplicationDbContext` classe deve esporre un costruttore pubblico con un `DbContextOptions<ApplicationDbContext>` parametro. Si tratta dello stesso modello usato nella sezione tradizionale ASP.NET Core precedente.

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

La `DbContextFactory` Factory può quindi essere usata in altri servizi tramite l'inserimento del costruttore. Ad esempio:

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

La Factory inserita può quindi essere usata per costruire istanze di DbContext nel codice del servizio. Ad esempio:

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

Si noti che le `DbContext` istanze create in questo modo **non** sono gestite dal provider di servizi dell'applicazione e pertanto devono essere eliminate dall'applicazione.

Per altre informazioni sull'uso di EF Core con blazer, vedere [ASP.NET Core Server Blazer con Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) .

## <a name="dbcontextoptions"></a>DbContextOptions

Il punto di partenza per tutte le `DbContext` configurazioni è <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Sono disponibili tre modi per ottenere questo generatore:

- In `AddDbContext` e metodi correlati
- In `OnConfiguring`
- Costruito in modo esplicito con `new`

Gli esempi di ognuno di questi elementi sono illustrati nelle sezioni precedenti. È possibile applicare la stessa configurazione indipendentemente dalla provenienza del generatore. Inoltre, `OnConfiguring` viene sempre chiamato indipendentemente dalla modalità di costruzione del contesto. Questo significa `OnConfiguring` che può essere usato per eseguire una configurazione aggiuntiva anche quando `AddDbContext` viene usato.

### <a name="configuring-the-database-provider"></a>Configurazione del provider di database

Ogni `DbContext` istanza deve essere configurata per l'utilizzo di un solo provider di database. (Le diverse istanze di un `DbContext` sottotipo possono essere utilizzate con provider di database diversi, ma una sola istanza deve utilizzarne solo una). Un provider di database viene configurato utilizzando una specifica `Use*` chiamata ". Ad esempio, per utilizzare il provider di database SQL Server:

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

Questi `Use*` "metodi sono metodi di estensione implementati dal provider di database. Questo significa che il pacchetto NuGet del provider di database deve essere installato prima di poter usare il metodo di estensione.

> [!TIP]
> I provider di database EF Core utilizzano in modo estensivo i [metodi di estensione](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods). Se il compilatore indica che non è possibile trovare un metodo, verificare che il pacchetto NuGet del provider sia installato e che sia presente `using Microsoft.EntityFrameworkCore;` nel codice.

La tabella seguente contiene esempi per i provider di database comuni.

| Sistema di database              | Configurazione di esempio                         | Pacchetto NuGet
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server o Azure SQL      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| Database in memoria EF Core   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| PostgreSQL                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/MariaDB *               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

* Questi provider di database non sono forniti da Microsoft. Per ulteriori informazioni sui provider di database, vedere [provider di database](xref:core/providers/index) .

> [!WARNING]
> Il EF Core database in memoria non è progettato per l'uso in produzione. Inoltre, potrebbe non essere la scelta migliore anche per i test. Per ulteriori informazioni, vedere [test del codice che usa EF Core](xref:core/testing/index) .

Per ulteriori informazioni sull'utilizzo delle stringhe di connessione con EF Core, vedere [stringhe di connessione](xref:core/miscellaneous/connection-strings) .

La configurazione facoltativa specifica del provider di database viene eseguita in un generatore aggiuntivo specifico del provider. Ad esempio, usando <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> per configurare i tentativi di resilienza della connessione quando ci si connette a SQL di Azure:

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
> Lo stesso provider di database viene usato per SQL Server e Azure SQL. È tuttavia consigliabile utilizzare la [resilienza della connessione](xref:core/miscellaneous/connection-resiliency) per la connessione a SQL Azure.

Per ulteriori informazioni sulla configurazione specifica del provider, vedere [provider di database](xref:core/providers/index) .

### <a name="other-dbcontext-configuration"></a>Altra configurazione di DbContext

`DbContext`È possibile concatenare un'altra configurazione prima o dopo (non fa alcuna differenza) la `Use*` chiamata. Ad esempio, per attivare la registrazione dei dati sensibili:

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

La tabella seguente contiene esempi di metodi comuni chiamati su `DbContextOptionsBuilder` .

| Metodo DbContextOptionsBuilder                                                             | Funzione                                                | Altre informazioni
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | Imposta il comportamento di rilevamento predefinito per le query              | [Comportamento di rilevamento delle query](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | Un modo semplice per ottenere i log di EF Core (EF Core 5,0 e versioni successive)    | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | Registra una `Microsoft.Extensions.Logging` Factory         | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | Include i dati dell'applicazione in eccezioni e registrazione         | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | Errori di query più dettagliati (a scapito delle prestazioni)  | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | Ignora o genera per avvisi e altri eventi               | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | Registra EF Core intercettori                              | [Registrazione, eventi e diagnostica](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | Usare proxy dinamici per il caricamento lazy                        | [Caricamento lazy](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | Usare proxy dinamici per il rilevamento delle modifiche                     | Presto disponibile...

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> e <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sono metodi di estensione dal pacchetto NuGet [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) . Questo tipo di ". La chiamata a UseSomething () è la modalità consigliata per configurare e/o utilizzare EF Core estensioni contenute in altri pacchetti.

### <a name="dbcontextoptions-versus-dbcontextoptionstcontext"></a>`DbContextOptions` contro `DbContextOptions<TContext>`

La maggior parte delle `DbContext` sottoclassi che accettano un oggetto `DbContextOptions` deve usare la variazione [generica](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` . Ad esempio:

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

Ciò garantisce che le opzioni corrette per il `DbContext` sottotipo specifico vengano risolte dall'inserimento di dipendenze, anche quando `DbContext` vengono registrati più sottotipi.

> [!TIP]
> Il DbContext non deve essere sealed, ma la procedura consigliata per le classi non è progettata per essere ereditata da.

Tuttavia, se il `DbContext` sottotipo è destinato a essere ereditato da, deve esporre un costruttore protetto che accetta un oggetto non generico `DbContextOptions` . Ad esempio:

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

In questo modo, più sottoclassi concrete possono chiamare il costruttore di base usando le diverse istanze generiche `DbContextOptions<TContext>` . Ad esempio:

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

Si noti che questo è esattamente lo stesso modello che si verifica quando si eredita `DbContext` direttamente da. Ovvero, `DbContext` per questo motivo il costruttore stesso accetta un oggetto non generico `DbContextOptions` .

Una `DbContext` sottoclasse progettata per essere creata ed ereditata da deve esporre entrambe le forme del costruttore. Ad esempio:

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

## <a name="design-time-dbcontext-configuration"></a>Configurazione DbContext della fase di progettazione

EF Core strumenti della fase di progettazione, ad esempio quelli per le [migrazioni di EF Core](xref:core/managing-schemas/migrations/index) , devono essere in grado di individuare e creare un'istanza di un tipo funzionante per `DbContext` raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database. Questo processo può essere automatico purché lo strumento possa creare facilmente il `DbContext` in modo da configurarlo in modo analogo a come verrebbe configurato in fase di esecuzione.

Anche se qualsiasi modello che fornisce le informazioni di configurazione necessarie a `DbContext` può funzionare in fase di esecuzione, gli strumenti che richiedono l'uso di `DbContext` in fase di progettazione possono funzionare solo con un numero limitato di modelli. Questi sono trattati in modo più dettagliato nella [creazione del contesto della fase di progettazione](xref:core/cli/dbcontext-creation).

## <a name="avoiding-dbcontext-threading-issues"></a>Evitare problemi di threading DbContext

Entity Framework Core non supporta l'esecuzione di più operazioni parallele nella stessa `DbContext` istanza. Sono incluse l'esecuzione parallela di query asincrone e qualsiasi utilizzo simultaneo esplicito da più thread. Pertanto, `await` le chiamate asincrone vengono sempre eseguite immediatamente oppure si utilizzano `DbContext` istanze separate per le operazioni eseguite in parallelo.

Quando EF Core rileva un tentativo di usare un' `DbContext` istanza simultaneamente, verrà visualizzato un `InvalidOperationException` messaggio con un messaggio simile al seguente:

> Una seconda operazione è stata avviata in questo contesto prima del completamento di un'operazione precedente. Questo problema è in genere causato da thread diversi che utilizzano la stessa istanza di DbContext, ma non è garantito che i membri di istanza siano thread-safe.

Quando l'accesso simultaneo non viene rilevato, può causare un comportamento indefinito, arresti anomali dell'applicazione e danneggiamento dei dati.

Si verificano errori comuni che possono causare inavvertitamente l'accesso simultaneo nella stessa `DbContext` istanza:

### <a name="asynchronous-operation-pitfalls"></a>Problemi di operazione asincrona

I metodi asincroni consentono EF Core di avviare operazioni che accedono al database in modo non bloccante. Tuttavia, se un chiamante non attende il completamento di uno di questi metodi e continua a eseguire altre operazioni su `DbContext` , lo stato di `DbContext` può essere, (e molto probabilmente sarà) danneggiato.

Attendi sempre EF Core metodi asincroni immediatamente.

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>Condivisione implicita delle istanze di DbContext tramite l'inserimento di dipendenze

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` Per impostazione predefinita, il metodo di estensione registra i tipi con una [durata con ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .

Ciò è sicuro dai problemi di accesso simultaneo nella maggior parte delle applicazioni ASP.NET Core perché è presente un solo thread che esegue ogni richiesta client in un determinato momento e perché ogni richiesta ottiene un ambito di inserimento delle dipendenze separato (e quindi un' `DbContext` istanza separata). Per il modello di hosting del server blazer, viene usata una richiesta logica per gestire il circuito utente di blazer e pertanto è disponibile solo un'istanza con ambito DbContext per ogni circuito utente se viene usato l'ambito di inserimento predefinito.

Qualsiasi codice che esegua in modo esplicito più thread in parallelo deve garantire che `DbContext` non venga mai eseguito l'accesso alle istanze simultaneamente.

Usando l'inserimento di dipendenze, è possibile ottenere questo risultato registrando il contesto come ambito e creando ambiti (usando `IServiceScopeFactory` ) per ogni thread o registrando `DbContext` come temporaneo (usando l'overload di `AddDbContext` che accetta un `ServiceLifetime` parametro).

## <a name="more-reading"></a>Altre informazioni

- Per altre informazioni sull'uso DI, vedere [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) .
- Per ulteriori informazioni, vedere [test](xref:core/testing/index) .
