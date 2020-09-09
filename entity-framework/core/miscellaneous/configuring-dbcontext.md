---
title: Configurazione di un DbContext-EF Core
description: Strategie per la configurazione di oggetti DbContext con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3e45199e6fc0c8c105ccb0bb03175b4b08716b3f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617793"
---
# <a name="configuring-a-dbcontext"></a>Configurazione di un DbContext

Questo articolo illustra i modelli di base per la configurazione di un `DbContext` tramite un `DbContextOptions` per connettersi a un database usando uno specifico provider di EF core e comportamenti facoltativi.

## <a name="design-time-dbcontext-configuration"></a>Configurazione DbContext della fase di progettazione

EF Core strumenti della fase di progettazione, ad esempio le [migrazioni](xref:core/managing-schemas/migrations/index) , devono essere in grado di individuare e creare un'istanza di un `DbContext` tipo funzionante per raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database. Questo processo può essere automatico purché lo strumento possa creare facilmente il `DbContext` in modo da configurarlo in modo analogo a come verrebbe configurato in fase di esecuzione.

Anche se qualsiasi modello che fornisce le informazioni di configurazione necessarie a `DbContext` può funzionare in fase di esecuzione, gli strumenti che richiedono l'uso di `DbContext` in fase di progettazione possono funzionare solo con un numero limitato di modelli. Queste informazioni sono descritte più dettagliatamente nella sezione relativa alla [creazione del contesto della fase di progettazione](xref:core/miscellaneous/cli/dbcontext-creation) .

## <a name="configuring-dbcontextoptions"></a>Configurazione di DbContextOptions

`DbContext` per eseguire qualsiasi operazione, è necessario disporre di un'istanza di `DbContextOptions` . L' `DbContextOptions` istanza contiene le informazioni di configurazione, ad esempio:

- Provider di database da usare, in genere selezionato richiamando un metodo come `UseSqlServer` o `UseSqlite` . Questi metodi di estensione richiedono il pacchetto del provider corrispondente, ad esempio `Microsoft.EntityFrameworkCore.SqlServer` o `Microsoft.EntityFrameworkCore.Sqlite` . I metodi sono definiti nello `Microsoft.EntityFrameworkCore` spazio dei nomi.
- Qualsiasi stringa di connessione o identificatore necessario dell'istanza del database, in genere passato come argomento al metodo di selezione del provider menzionato in precedenza
- Tutti i selettori di comportamento facoltativi a livello di provider, in genere concatenati all'interno della chiamata al metodo di selezione del provider
- Tutti i selettori di comportamento di EF Core generali, in genere concatenati dopo o prima del metodo selettore provider

Nell'esempio seguente viene configurato `DbContextOptions` per l'utilizzo del provider SQL Server, una connessione contenuta nella `connectionString` variabile, un timeout del comando a livello di provider e un selettore di comportamento EF core che rende tutte le query eseguite in `DbContext` [Nessun rilevamento](xref:core/querying/tracking#no-tracking-queries) per impostazione predefinita:

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> I metodi del selettore del provider e altri metodi di selezione dei comportamenti indicati in precedenza sono metodi di estensione nelle `DbContextOptions` classi di opzioni specifiche del provider o. Per poter accedere a questi metodi di estensione, potrebbe essere necessario disporre di uno spazio dei nomi (in genere `Microsoft.EntityFrameworkCore` ) nell'ambito e includere altre dipendenze del pacchetto nel progetto.

`DbContextOptions`È possibile fornire l'oggetto `DbContext` eseguendo l'override del `OnConfiguring` metodo o esternamente tramite un argomento del costruttore.

Se vengono usati entrambi, `OnConfiguring` viene applicato per ultimo e è possibile sovrascrivere le opzioni fornite all'argomento del costruttore.

### <a name="constructor-argument"></a>Argomento del costruttore

Il costruttore può semplicemente accettare un `DbContextOptions` come segue:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> Il costruttore di base di DbContext accetta anche la versione non generica di `DbContextOptions` , ma non è consigliabile usare la versione non generica per le applicazioni con più tipi di contesto.

L'applicazione può ora passare `DbContextOptions` quando si crea un'istanza di un contesto, come indicato di seguito:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>Onconfigurazione

È anche possibile inizializzare l'oggetto `DbContextOptions` all'interno del contesto stesso. Sebbene sia possibile usare questa tecnica per la configurazione di base, in genere è comunque necessario ottenere determinati dettagli di configurazione dall'esterno, ad esempio una stringa di connessione del database. Questa operazione può essere eseguita con un'API di configurazione o qualsiasi altro mezzo.

Per eseguire l'inizializzazione `DbContextOptions` all'interno del contesto, eseguire l'override del `OnConfiguring` metodo e chiamare i metodi sull'oggetto fornito `DbContextOptionsBuilder` :

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

Un'applicazione può semplicemente creare un'istanza di tale contesto senza passare alcun elemento al costruttore:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Questo approccio non si presta ai test, a meno che i test non siano destinati al database completo.

### <a name="using-dbcontext-with-dependency-injection"></a>Uso di DbContext con l'inserimento di dipendenze

EF Core supporta `DbContext` l'utilizzo di con un contenitore di inserimento delle dipendenze. Il tipo di DbContext può essere aggiunto al contenitore del servizio usando il `AddDbContext<TContext>` metodo.

`AddDbContext<TContext>` renderà il tipo di DbContext, `TContext` e l'oggetto corrispondente `DbContextOptions<TContext>` disponibile per l'inserimento dal contenitore del servizio.

Per ulteriori informazioni sull'inserimento delle dipendenze, vedere [più](#more-reading) avanti.

Aggiunta dell' `DbContext` inserimento delle dipendenze:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

A tale scopo, è necessario aggiungere un [argomento del costruttore](#constructor-argument) al tipo DbContext che accetta `DbContextOptions<TContext>` .

Codice contesto:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Codice dell'applicazione (in ASP.NET Core):

``` csharp
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

Codice dell'applicazione (usando direttamente ServiceProvider, meno comune):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>Evitare problemi di threading DbContext

Entity Framework Core non supporta l'esecuzione di più operazioni parallele nella stessa `DbContext` istanza. Sono incluse l'esecuzione parallela di query asincrone e qualsiasi utilizzo simultaneo esplicito da più thread. Pertanto, `await` le chiamate asincrone vengono sempre eseguite immediatamente oppure si utilizzano `DbContext` istanze separate per le operazioni eseguite in parallelo.

Quando EF Core rileva un tentativo di usare un' `DbContext` istanza simultaneamente, verrà visualizzato un `InvalidOperationException` messaggio con un messaggio simile al seguente:

> Una seconda operazione è stata avviata in questo contesto prima del completamento di un'operazione precedente. Questo problema è in genere causato da thread diversi che utilizzano la stessa istanza di DbContext, ma non è garantito che i membri di istanza siano thread-safe.

Quando l'accesso simultaneo non viene rilevato, può causare un comportamento indefinito, arresti anomali dell'applicazione e danneggiamento dei dati.

Si verificano errori comuni che possono causare inavvertitamente l'accesso simultaneo nella stessa `DbContext` istanza:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Si dimentica di attendere il completamento di un'operazione asincrona prima di avviare qualsiasi altra operazione nello stesso DbContext

I metodi asincroni consentono EF Core di avviare operazioni che accedono al database in modo non bloccante. Tuttavia, se un chiamante non attende il completamento di uno di questi metodi e continua a eseguire altre operazioni su `DbContext` , lo stato di `DbContext` può essere, (e molto probabilmente sarà) danneggiato.

Attendi sempre EF Core metodi asincroni immediatamente.  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Condivisione implicita delle istanze di DbContext in più thread tramite l'inserimento di dipendenze

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` Per impostazione predefinita, il metodo di estensione registra i tipi con una [durata con ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .

Ciò è sicuro dai problemi di accesso simultaneo nella maggior parte delle applicazioni ASP.NET Core perché è presente un solo thread che esegue ogni richiesta client in un determinato momento e perché ogni richiesta ottiene un ambito di inserimento delle dipendenze separato (e quindi un' `DbContext` istanza separata). Per il modello di hosting del server blazer, viene usata una richiesta logica per gestire il circuito utente di blazer e pertanto è disponibile solo un'istanza con ambito DbContext per ogni circuito utente se viene usato l'ambito di inserimento predefinito.

Qualsiasi codice che esegua in modo esplicito più thread in parallelo deve garantire che `DbContext` non venga mai eseguito l'accesso alle istanze simultaneamente.

Usando l'inserimento di dipendenze, è possibile ottenere questo risultato registrando il contesto come ambito e creando ambiti (usando `IServiceScopeFactory` ) per ogni thread o registrando `DbContext` come temporaneo (usando l'overload di `AddDbContext` che accetta un `ServiceLifetime` parametro).

## <a name="more-reading"></a>Altre informazioni

- Per altre informazioni sull'uso DI, vedere [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) .
- Per ulteriori informazioni, vedere [test](xref:core/miscellaneous/testing/index) .
