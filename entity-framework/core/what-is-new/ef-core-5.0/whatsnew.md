---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: ab6a49fa0b23aff83ee64446e498b7e94c632020
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023509"
---
# <a name="whats-new-in-ef-core-50"></a>Novità di EF Core 5,0

Nell'elenco seguente sono incluse le nuove funzionalità principali di EF Core 5,0. Per l'elenco completo dei problemi nella versione, vedere Microsoft [Issue Tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).

Come versione principale, EF Core 5,0 contiene anche alcune [modifiche di rilievo](xref:core/what-is-new/ef-core-5.0/breaking-changes), ovvero miglioramenti dell'API o modifiche comportamentali che possono avere un impatto negativo sulle applicazioni esistenti.

## <a name="many-to-many"></a>Molti-a-molti

EF Core 5,0 supporta relazioni molti-a-molti senza eseguire in modo esplicito il mapping della tabella di join.

Si considerino, ad esempio, i tipi di entità seguenti:

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

EF Core 5,0 riconosce questa relazione come una relazione molti-a-molti per convenzione e crea automaticamente una `PostTag` tabella di join nel database. I dati possono essere sottoposti a query e aggiornati senza fare riferimento in modo esplicito alla tabella di join, semplificando notevolmente il codice. La tabella di join può comunque essere personalizzata e sottoposta a query in modo esplicito, se necessario.

Per ulteriori informazioni, [vedere la documentazione completa su molti-a-molti](xref:core/modeling/relationships#many-to-many).

## <a name="split-queries"></a>Suddividere le query

A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ. In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso. Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.

EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL. Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query. È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.

Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

Con le query Split viene invece generato il codice SQL seguente:

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

È possibile abilitare le query Split inserendo il nuovo `AsSplitQuery` operatore in un punto qualsiasi della query LINQ o globalmente nell'oggetto del modello `OnConfiguring` . Per ulteriori informazioni, [vedere la documentazione completa sulle query suddivise](xref:core/querying/single-split-queries).

## <a name="simple-logging-and-improved-diagnostics"></a>Registrazione semplice e diagnostica migliorata

EF Core 5,0 introduce un modo semplice per impostare la registrazione tramite il nuovo `LogTo` metodo. Il codice seguente causerà la scrittura dei messaggi di registrazione nella console, inclusi tutti i SQL generati da EF Core:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

Inoltre, è ora possibile chiamare `ToQueryString` su qualsiasi query LINQ, recuperando il SQL che verrà eseguito dalla query:

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

Infine, vari tipi di EF Core sono stati inseriti con una `DebugView` Proprietà migliorata che fornisce una visualizzazione dettagliata degli elementi interni. Ad esempio, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> è possibile consultare per verificare esattamente quali entità vengono registrate in un determinato momento.

Per ulteriori informazioni, [vedere la documentazione relativa alla registrazione e all'intercettazione](xref:core/logging-events-diagnostics/index).

## <a name="filtered-include"></a>Inclusione filtrato

Il `Include` Metodo ora supporta il filtraggio delle entità incluse:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".

Per ulteriori informazioni, [vedere la documentazione completa sull'inclusione filtrata](xref:core/querying/related-data/eager#filtered-include).

## <a name="table-per-type-tpt-mapping"></a>Mapping tabella per tipo (TPT)

Per impostazione predefinita, EF Core esegue il mapping di una gerarchia di ereditarietà di tipi .NET a una singola tabella di database. Questa operazione è nota come mapping tabella per gerarchia (TPH). EF Core 5,0 consente inoltre di eseguire il mapping di ogni tipo .NET in una gerarchia di ereditarietà a una tabella di database differente. noto come mapping tabella per tipo (TPT).

Si consideri, ad esempio, questo modello con una gerarchia mappata:

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

Con TPT, viene creata una tabella di database per ogni tipo nella gerarchia:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

Per ulteriori informazioni, [vedere la documentazione completa su TPT](xref:core/modeling/inheritance).

## <a name="flexible-entity-mapping"></a>Mapping di entità flessibili

I tipi di entità vengono in genere mappati a tabelle o viste in modo che EF Core effettuerà il pull del contenuto della tabella o della vista durante l'esecuzione di query per quel tipo. EF Core 5,0 aggiunge opzioni di mapping aggiuntive, in cui è possibile eseguire il mapping di un'entità a una query SQL (denominata "query di definizione") o a una funzione con valori di tabella (TVF):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

È anche possibile eseguire il mapping delle funzioni con valori di tabella a un metodo .NET anziché a un DbSet, consentendo il passaggio di parametri. il mapping può essere impostato con <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> .

Infine, è ora possibile eseguire il mapping di un'entità a una vista durante l'esecuzione di query (o a una funzione o la definizione di una query), ma a una tabella durante l'aggiornamento:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>Tipi di entità e contenitori di proprietà di tipo condiviso

EF Core 5,0 consente di eseguire il mapping dello stesso tipo CLR a più tipi di entità diversi. tali tipi sono noti come tipi di entità di tipo condiviso. Sebbene sia possibile usare qualsiasi tipo CLR con questa funzionalità, .NET `Dictionary` offre un caso d'uso particolarmente interessante, che viene chiamato "sacchetti delle proprietà":

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

È quindi possibile eseguire query su queste entità e aggiornarle esattamente come i normali tipi di entità con il proprio tipo CLR dedicato. Ulteriori informazioni sono disponibili nella documentazione relativa a [contenitori di proprietà](xref:core/modeling/shadow-properties).

## <a name="required-11-dependents"></a>Dipendenti 1:1 richiesti

In EF Core 3,1, l'entità finale dipendente di una relazione uno-a-uno è sempre considerata facoltativa. Questo è stato più evidente quando si usano le entità di proprietà, perché tutte le colonne dell'entità di proprietà sono state create come nullable nel database, anche se sono state configurate come richieste nel modello.

In EF Core 5,0, una navigazione a un'entità di proprietà può essere configurata come dipendente obbligatorio. Ad esempio:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5,0 introduce `AddDbContextFactory` e `AddPooledDbContextFactory` per registrare una factory per la creazione di istanze DbContext nel contenitore di inserimento delle dipendenze dell'applicazione (D.I.). questa operazione può essere utile quando il codice dell'applicazione deve creare ed eliminare manualmente le istanze del contesto.

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

A questo punto, è possibile inserire i servizi delle applicazioni come ASP.NET Core controller `IDbContextFactory<TContext>` e usarli per creare un'istanza delle istanze di contesto:

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

Per ulteriori informazioni, [vedere la documentazione completa su DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).

## <a name="sqlite-table-rebuilds"></a>Ricompilazioni tabella SQLite

Rispetto ad altri database, SQLite è relativamente limitato nelle funzionalità di manipolazione dello schema; l'eliminazione di una colonna da una tabella esistente, ad esempio, non è supportata. EF Core 5,0 ovviare a queste limitazioni creando automaticamente una nuova tabella, copiando i dati dalla vecchia tabella, eliminando la tabella precedente e rinominando quella nuova. Questa operazione "ricompila" la tabella e consente di applicare in modo sicuro le operazioni di migrazione non supportate in precedenza.

Per informazioni dettagliate sulle operazioni di migrazione supportate tramite ricompilazioni di tabella, [vedere questa pagina della documentazione](xref:core/providers/sqlite/limitations#migrations-limitations).

## <a name="database-collations"></a>Regole di confronto di database

EF Core 5,0 introduce il supporto per specificare le regole di confronto del testo a livello di database, di colonna o di query. Questo consente di configurare la distinzione tra maiuscole e minuscole e altri aspetti testuali in modo flessibile e non compromettere le prestazioni di esecuzione delle query.

Il codice seguente, ad esempio, configurerà la `Name` colonna in modo da fare distinzione tra maiuscole e minuscole in SQL Server e tutti gli indici creati sulla colonna funzioneranno in modo appropriato:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

Per ulteriori informazioni, [vedere la documentazione completa sulle regole di confronto e la distinzione tra maiuscole e minuscole](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="event-counters"></a>Contatori di eventi

EF Core 5,0 espone i [contatori degli eventi](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) che possono essere usati per tenere traccia delle prestazioni dell'applicazione e individuare varie anomalie. È sufficiente connettersi a un processo che esegue EF con lo strumento [DotNet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) :

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

Per ulteriori informazioni, [vedere la documentazione completa sui contatori degli eventi](xref:core/logging-events-diagnostics/event-counters).

## <a name="other-features"></a>Altre funzionalità

### <a name="model-building"></a>Creazione di modelli

* Sono state introdotte API per la creazione di modelli per semplificare la configurazione degli [operatori di confronto dei valori](xref:core/modeling/value-comparers).
* Le colonne calcolate ora possono essere configurate come [ *archiviate* o *virtuali*](xref:core/modeling/generated-properties#computed-columns).
* La precisione e la scala possono ora essere configurate [tramite l'API Fluent](xref:core/modeling/entity-properties#precision-and-scale).
* Sono state introdotte nuove API per la creazione di modelli per le [proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).
* Sono state introdotte nuove API per la creazione di modelli per i campi, in modo analogo alle proprietà.
* È ora possibile eseguire il mapping dei tipi .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) e [IPAddress](/dotnet/api/system.net.ipaddress) alle colonne stringa del database.
* È ora possibile configurare un campo sottostante tramite [il nuovo `[BackingField]` attributo](xref:core/modeling/backing-field).
* Sono ora consentiti i campi di supporto Nullable, per offrire un supporto migliore per le impostazioni predefinite generate dall'archivio in cui il valore predefinito di CLR non è un valore sentinella valido (rilevante `bool` ).
* Un nuovo `[Index]` attributo può essere usato in un tipo di entità per specificare un indice, anziché usare l'API Fluent.
* Un nuovo `[Keyless]` attributo può essere utilizzato per configurare un tipo di entità [senza chiave](xref:core/modeling/keyless-entity-types).
* Per impostazione predefinita, [EF Core considera ora i discriminatori come *completi*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), ovvero prevede di non visualizzare mai i valori del discriminatore non configurati dall'applicazione nel modello. Questo consente di migliorare le prestazioni e può essere disabilitato se la colonna del discriminatore potrebbe contenere valori sconosciuti.

### <a name="query"></a>Query

* Le eccezioni di errore di conversione delle query ora contengono motivi più espliciti relativi ai motivi dell'errore, per facilitare l'individuazione del problema.
* Le query senza rilevamento possono ora eseguire la [risoluzione delle identità](xref:core/querying/tracking#identity-resolution), evitando la restituzione di più istanze di entità per lo stesso oggetto di database.
* Aggiunta del supporto per GroupBy con aggregazioni condizionali, ad `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))` esempio.
* Aggiunta del supporto per la conversione dell'operatore DISTINCT sugli elementi del gruppo prima dell'aggregazione.
* Traduzione di [`Reverse`](/dotnet/api/system.linq.queryable.reverse) .
* Traduzione migliorata `DateTime` per SQL Server (ad esempio [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) , [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) ).
* Conversione di nuovi metodi in matrici di byte (ad esempio [`Contains`](/dotnet/api/system.linq.enumerable.contains) , [`Length`](/dotnet/api/system.array.length) , [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal) ).
* Conversione di alcuni operatori bit per bit aggiuntivi, ad esempio il complemento a due.
* Conversione di `FirstOrDefault` su stringhe.
* Migliore conversione delle query in termini di semantica null, ottenendo query più rigorose ed efficienti.
* È ora possibile aggiungere annotazioni alle funzioni mappate dall'utente per controllare la propagazione Null, ottenendo nuovamente query più efficienti e più efficienti.
* I blocchi di CASE SQL contenenti sono ora molto più concisi.
* [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql)È ora possibile chiamare la funzione SQL Server nelle query tramite il nuovo [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) metodo.
* `EnableDetailedErrors` aggiunge [ulteriori dettagli alle eccezioni](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).

### <a name="saving"></a>Saving

* [Intercettazione](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) ed [eventi](xref:core/logging-events-diagnostics/events)di SaveChanges.
* Sono state introdotte API per il controllo di [salvataggio di transazione](xref:core/saving/transactions#savepoints). Inoltre, EF Core creerà automaticamente un salvataggio quando `SaveChanges` viene chiamato e una transazione è già in corso e ne viene eseguito il rollback in caso di errore.
* Un ID transazione può essere impostato in modo esplicito dall'applicazione, consentendo una correlazione più semplice degli eventi di transazione nella registrazione e altrove.
* Le dimensioni massime del batch predefinite per SQL Server sono state modificate in 42 in base a un'analisi delle prestazioni in batch.

### <a name="migrations-and-scaffolding"></a>Migrazioni e ponteggi

* È ora possibile [escludere le tabelle dalle migrazioni](xref:core/modeling/entity-types#excluding-from-migrations).
* Un nuovo [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) comando ora Mostra le migrazioni che non sono state ancora applicate al database ( [`Get-Migration`](xref:core/cli/powershell#get-migration) esegue la stessa operazione nella Console di gestione pacchetti).
* Gli script delle migrazioni ora contengono istruzioni di transazione laddove appropriate per migliorare la gestione dei casi in cui l'applicazione di migrazione non riesce.
* Le colonne per le classi di base senza mapping sono ora ordinate dopo altre colonne per i tipi di entità mappati. Si noti che questo influisca solo sulle tabelle appena create. l'ordine delle colonne per le tabelle esistenti rimane invariato.
* La generazione della migrazione ora può essere consapevole se la migrazione generata è idempotente e se l'output verrà eseguito immediatamente o generato come uno script.
* Sono stati aggiunti nuovi parametri della riga di comando per specificare gli spazi dei nomi nelle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e nell' [impalcatura](xref:core/managing-schemas/scaffolding#directories-and-namespaces).
* Il comando [DotNet EF database Update](xref:core/cli/dotnet#dotnet-ef-database-update) ora accetta un nuovo `--connection` parametro per specificare la stringa di connessione.
* L'impalcatura dei database esistenti ora singolari i nomi di tabella, quindi le tabelle denominate e verranno sottoponite `People` `Addresses` a ponteggi ai tipi di entità chiamati `Person` e `Address` . [I nomi dei database originali possono comunque essere conservati](xref:core/managing-schemas/scaffolding#preserving-names).
* La nuova [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) opzione può indicare EF core da escludere `OnModelConfiguring` durante l'impalcatura di un modello.

### <a name="cosmos"></a>Cosmos

* [Le impostazioni di connessione Cosmos](xref:core/providers/cosmos/index#cosmos-options) sono state espanse.
* La concorrenza ottimistica è ora [supportata in Cosmos tramite l'uso di ETag](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).
* Il nuovo `WithPartitionKey` metodo consente di includere la [chiave di partizione](xref:core/providers/cosmos/index#partition-keys) Cosmos sia nel modello che nelle query.
* I metodi di [`Contains`](/dotnet/api/system.string.contains) stringa [`StartsWith`](/dotnet/api/system.string.startswith) e [`EndsWith`](/dotnet/api/system.string.endswith) sono ora tradotti per Cosmos.
* L' `is` operatore C# è ora convertito in Cosmos.

### <a name="sqlite"></a>SQLite

* Sono ora supportate le colonne calcolate.
* Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.
* L'inizializzazione di SqliteConnection è ora Lazy.

### <a name="other"></a>Altri

* È possibile generare proxy di rilevamento delle modifiche che implementano automaticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) e [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Questo approccio offre un approccio alternativo al rilevamento delle modifiche che non esegue l'analisi delle modifiche quando `SaveChanges` viene chiamato.
* <xref:System.Data.Common.DbConnection>È ora possibile modificare una stringa di connessione o in un DbContext già inizializzato.
* Il nuovo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> metodo. 0 # Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) cancella il DbContext di tutte le entità rilevate. Questa situazione in genere non è necessaria quando si usa la procedura consigliata per la creazione di una nuova istanza del contesto di breve durata per ogni unità di lavoro. Tuttavia, se è necessario reimpostare lo stato di un'istanza di DbContext, l'uso del nuovo `Clear()` metodo è più efficiente e affidabile rispetto alla disconnessione di massa per tutte le entità.
* Gli strumenti da riga di comando EF Core ora configurano automaticamente le `ASPNETCORE_ENVIRONMENT`  `DOTNET_ENVIRONMENT` variabili di ambiente e in "Development". In questo modo, quando si usa l'host generico in linea con l'esperienza ASP.NET Core durante lo sviluppo.
* Gli argomenti della riga di comando personalizzati possono essere propagati in <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> , consentendo alle applicazioni di controllare la modalità di creazione e inizializzazione del contesto.
* Il fattore di riempimento dell'indice ora può essere [configurato in SQL Server](xref:core/providers/sql-server/indexes#fill-factor).
* La nuova <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> proprietà può essere utilizzata per distinguere quando si utilizza un provider relazionale e un provider non relazionale (ad esempio InMemory).
