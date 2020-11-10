---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3efa883cdfac1ecd412112ef06c7763f1a7e12f1
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429247"
---
# <a name="whats-new-in-ef-core-50"></a>Novità di EF Core 5,0

Tutte le funzionalità pianificate per EF Core 5,0 sono state completate. Questa pagina contiene una panoramica delle modifiche interessanti introdotte in ogni anteprima.

Questa pagina non duplica il [piano per EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan). Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.

## <a name="rc1"></a>RC1

### <a name="many-to-many"></a>Molti-a-molti

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

Si noti che `Post` contiene una raccolta di `Tags` e `Tag` contiene una raccolta di `Posts` . EF Core 5,0 riconosce questa relazione come una relazione molti-a-molti per convenzione. Questo significa che non è necessario alcun codice in `OnModelCreating` :

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

Quando `EnsureCreated` si utilizzano le migrazioni (o) per creare il database, EF Core creerà automaticamente la tabella di join. Ad esempio, in SQL Server per questo modello, EF Core genera:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

La creazione e l'associazione di `Tag` `Post` entità e determinano la creazione automatica di aggiornamenti della tabella di join. Esempio:

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

Dopo aver inserito i post e i tag, EF creerà automaticamente le righe nella tabella di join. Ad esempio, in SQL Server:

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

Per le query, includere e altre operazioni di query funzionano esattamente come per qualsiasi altra relazione. Esempio:

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

Il SQL generato usa la tabella di join automaticamente per ripristinare tutti i tag correlati:

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

A differenza di EF6, EF Core consente la personalizzazione completa della tabella di join. Nel codice seguente, ad esempio, viene configurata una relazione molti-a-molti che include anche le navigazioni per l'entità di join e in cui l'entità join contiene una proprietà payload:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

> [!NOTE]
> Il supporto per l'impalcatura di relazioni molti-a-molti dal database non è ancora stato aggiunto. Vedere la pagina relativa al [rilevamento del problema](https://github.com/dotnet/efcore/issues/22475).

### <a name="map-entity-types-to-queries"></a>Eseguire il mapping di tipi di entità alle query

I tipi di entità vengono in genere mappati a tabelle o viste in modo che EF Core effettuerà il pull del contenuto della tabella o della vista durante l'esecuzione di query per quel tipo. EF Core 5,0 consente il mapping di un tipo di entità a una "query di definizione". (Questo è stato parzialmente supportato nelle versioni precedenti, ma è molto migliorato e presenta una sintassi diversa in EF Core 5,0).

Si considerino, ad esempio, due tabelle. una con post moderni; l'altro con i post legacy. La tabella Posts moderna include alcune colonne aggiuntive, ma ai fini dell'applicazione si desidera che sia i post moderni che quelli legacy vengano combinati e mappati a un tipo di entità con tutte le proprietà necessarie:

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

In EF Core 5,0, `ToSqlQuery` può essere utilizzato per eseguire il mapping di questo tipo di entità a una query che esegue il pull e combina le righe da entrambe le tabelle:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

Si noti che la `legacy_posts` tabella non contiene una `Category` colonna, pertanto viene invece sintetizzato un valore predefinito per tutti i post legacy.

Questo tipo di entità può quindi essere utilizzato in modo normale per le query LINQ. Ad esempio, query LINQ:

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

Genera il codice SQL seguente in SQLite:

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

Si noti che la query configurata per il tipo di entità viene utilizzata come avvio per la composizione della query LINQ completa.

### <a name="event-counters"></a>Contatori di eventi

I [contatori di eventi .NET](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) rappresentano un modo per esporre in modo efficiente le metriche delle prestazioni da un'applicazione. EF Core 5,0 include i contatori di eventi nella `Microsoft.EntityFrameworkCore` categoria. Esempio:

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

Ciò indica ai contatori DotNet di iniziare a raccogliere gli eventi di EF Core per il processo 49496. Verrà generato un output simile al seguente nella console:

```console
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

### <a name="property-bags"></a>Contenitori delle proprietà

EF Core 5,0 consente di eseguire il mapping dello stesso tipo CLR a più tipi di entità diversi. Tali tipi sono noti come tipi di entità di tipo condiviso. Questa funzionalità combinata con le proprietà dell'indicizzatore (inclusa nella versione Preview 1) consente di usare i contenitori di proprietà come tipo di entità.

Il DbContext seguente, ad esempio, configura il tipo BCL `Dictionary<string, object>` come tipo di entità di tipo condiviso per i prodotti e le categorie.

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

Gli oggetti dizionario ("contenitori di proprietà") possono ora essere aggiunti al contesto come istanze di entità e salvati. Esempio:

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

Queste entità possono quindi essere sottoposte a query e aggiornate in modo normale:

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a>Intercettazione ed eventi di SaveChanges

EF Core 5,0 introduce sia gli eventi .NET che un intercettore EF Core attivato quando viene chiamato SaveChanges.

Gli eventi sono semplici da usare. Per esempio:

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

Si noti che:

* Il mittente dell'evento è l' `DbContext` istanza
* Gli argomenti per l' `SavedChanges` evento contengono il numero di entità salvate nel database

L'intercettore è definito da `ISaveChangesInterceptor` , ma spesso è conveniente da ereditare da `SaveChangesInterceptor` per evitare l'implementazione di ogni metodo. Esempio:

```csharp
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

Si noti che:

* L'intercettore dispone di metodi Sync e Async. Questa operazione può essere utile se è necessario eseguire operazioni di I/O asincrone, ad esempio la scrittura in un server di controllo.
* L'intercettore consente di ignorare SaveChanges utilizzando il `InterceptionResult` meccanismo comune a tutti gli intercettori.

Il lato negativo degli intercettori è che devono essere registrati nel DbContext quando viene costruito. Esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

Al contrario, gli eventi possono essere registrati nell'istanza di DbContext in qualsiasi momento.

### <a name="exclude-tables-from-migrations"></a>Escludi tabelle dalle migrazioni

A volte è utile avere un solo tipo di entità mappato in più oggetti DbContext. Ciò vale soprattutto quando si usano i [contesti delimitati](https://www.martinfowler.com/bliki/BoundedContext.html), per i quali è comune avere un tipo DbContext diverso per ogni contesto delimitato.

Un tipo, ad esempio, `User` può essere necessario sia per un contesto di autorizzazione che per un contesto di Reporting. Se viene apportata una modifica al `User` tipo, le migrazioni per entrambi oggetti DbContext tenterà di aggiornare il database. Per evitare questo problema, è possibile configurare il modello per uno dei contesti in modo da escludere la tabella dalle relative migrazioni.

Nel codice riportato di seguito, il genererà le `AuthorizationContext` migrazioni per le modifiche apportate alla `Users` tabella, ma `ReportingContext` non ne impedirà lo scontro tra le migrazioni.

```csharp
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a>Dipendenti 1:1 richiesti

In EF Core 3,1, l'entità finale dipendente di una relazione uno-a-uno è sempre considerata facoltativa. Questo è stato più evidente quando si usano entità di proprietà. Si consideri, ad esempio, il modello e la configurazione seguenti:

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

Ciò comporta la creazione della migrazione della tabella seguente per SQLite:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Si noti che tutte le colonne ammettono valori null, anche se alcune delle `HomeAddress` proprietà sono state configurate in modo obbligatorio. Inoltre, quando si esegue una query per un oggetto `Person` , se tutte le colonne per l'indirizzo di casa o di lavoro sono null, EF Core lascerà le `HomeAddress` proprietà e/o `WorkAddress` come null, anziché impostare un'istanza vuota di `Address` .

In EF Core 5,0, la `HomeAddress` navigazione può ora essere configurata come dipendente obbligatorio. Esempio:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

Nella tabella creata dalle migrazioni verranno ora incluse colonne che non ammettono i valori null per le proprietà obbligatorie del dipendente richiesto:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Inoltre, EF Core ora genererà un'eccezione se viene effettuato un tentativo di salvare un proprietario con una dipendenza obbligatoria da null. In questo esempio EF Core genererà un'eccezione quando si tenta di salvare un oggetto `Person` con un valore null `HomeAddress` .

Infine, EF Core creerà comunque un'istanza di un oggetto dipendente richiesto anche quando tutte le colonne per il dipendente richiesto hanno valori null.

### <a name="options-for-migration-generation"></a>Opzioni per la generazione della migrazione

EF Core 5,0 introduce un maggiore controllo sulla generazione delle migrazioni per scopi diversi. con la capacità di:

* Verificare se la migrazione viene generata per uno script o per l'esecuzione immediata
* Verificare se è in corso la generazione di uno script idempotente
* Sapere se lo script deve escludere le istruzioni Transaction (vedere gli _script di migrazione con le transazioni_ seguenti).

Questo comportamento viene specificato da un' `MigrationsSqlGenerationOptions` enumerazione, che ora può essere passata a `IMigrator.GenerateScript` .

In questo lavoro è anche inclusa una migliore generazione di script idempotente con chiamate a `EXEC` su SQL Server quando necessario. Questo lavoro consente anche miglioramenti simili per gli script generati da altri provider di database, incluso PostgreSQL.

### <a name="migrations-scripts-with-transactions"></a>Script di migrazione con transazioni

Gli script SQL generati dalle migrazioni ora contengono istruzioni per avviare ed eseguire il commit delle transazioni nel modo appropriato per la migrazione. Ad esempio, lo script di migrazione seguente è stato generato da due migrazioni. Si noti che ogni migrazione è ora applicata all'interno di una transazione.

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

Come indicato nella sezione precedente, l'utilizzo delle transazioni può essere disabilitato se le transazioni devono essere gestite in modo diverso.

### <a name="see-pending-migrations"></a>Vedere migrazioni in sospeso

Questa funzionalità è stata fornita dalla community da [@Psypher9](https://github.com/Psypher9) . Molti ringraziamenti per il contributo.

Il `dotnet ef migrations list` comando ora Mostra le migrazioni non ancora applicate al database. Esempio:

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

Inoltre, è ora disponibile un `Get-Migration` comando per la console di gestione pacchetti con la stessa funzionalità.

### <a name="modelbuilder-api-for-value-comparers"></a>API ModelBuilder per gli operatori di confronto dei valori

EF Core proprietà per i tipi modificabili personalizzati [richiedono un operatore di confronto del valore](xref:core/modeling/value-comparers) per rilevare correttamente le modifiche delle proprietà. Questa ora può essere specificata come parte della configurazione della conversione del valore per il tipo. Esempio:

```csharp
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a>Metodi di EntityEntry TryGetValue

Questa funzionalità è stata fornita dalla community da [@m4ss1m0g](https://github.com/m4ss1m0g) . Molti ringraziamenti per il contributo.

`TryGetValue`È stato aggiunto un metodo a `EntityEntry.CurrentValues` e `EntityEntry.OriginalValues` . Ciò consente di richiedere il valore di una proprietà senza prima verificare se è stato eseguito il mapping della proprietà nel modello EF. Esempio:

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a>Dimensioni massime batch predefinite per SQL Server

A partire da EF Core 5,0, le dimensioni massime del batch predefinite per SaveChanges in SQL Server ora sono 42. Come è noto, si tratta anche della risposta alla domanda finale della vita, dell'universo e di tutto. Tuttavia, si tratta probabilmente di una coincidenza, poiché il valore è stato ottenuto tramite l' [analisi delle prestazioni in batch](https://github.com/dotnet/efcore/issues/9270). Non si ritiene che sia stata individuata una forma di domanda definitiva, anche se è piuttosto plausibile che la terra sia stata creata per comprendere il motivo per cui SQL Server funziona in modo corretto.

### <a name="default-environment-to-development"></a>Ambiente predefinito per lo sviluppo

Gli strumenti da riga di comando EF Core ora configurano automaticamente le `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` variabili di ambiente e in "Development". In questo modo, quando si usa l'host generico in linea con l'esperienza ASP.NET Core durante lo sviluppo. Vedere [#19903](https://github.com/dotnet/efcore/issues/19903).

### <a name="better-migrations-column-ordering"></a>Ordinamento delle colonne per migrazioni migliori

Le colonne per le classi di base senza mapping sono ora ordinate dopo altre colonne per i tipi di entità mappati. Si noti che questo influisca solo sulle tabelle appena create. L'ordine delle colonne per le tabelle esistenti rimane invariato. Vedere [#11314](https://github.com/dotnet/efcore/issues/11314).

### <a name="query-improvements"></a>Miglioramenti delle query

EF Core 5,0 RC1 contiene alcuni miglioramenti aggiuntivi per la conversione delle query:

* Conversione di `is` in Cosmos. vedere [#16391](https://github.com/dotnet/efcore/issues/16391)
* È ora possibile annotare le funzioni mappate dall'utente per controllare la propagazione Null. vedere [#19609](https://github.com/dotnet/efcore/issues/19609)
* Supporto per la conversione di GroupBy con aggregazioni condizionali. vedere [#11711](https://github.com/dotnet/efcore/issues/11711)
* Traduzione dell'operatore DISTINCT sull'elemento Group prima dell'aggregazione. vedere [#17376](https://github.com/dotnet/efcore/issues/17376)

### <a name="model-building-for-fields"></a>Creazione di modelli per i campi

Infine, per RC1, EF Core ora consente l'uso dei metodi lambda in ModelBuilder per i campi e le proprietà. Se, ad esempio, si è contrari alle proprietà per qualche motivo e si decide di usare campi pubblici, è ora possibile eseguire il mapping di questi campi usando i generatori di espressioni lambda:

```csharp
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

Sebbene questa operazione sia ora possibile, non è consigliabile eseguire questa operazione. Si noti inoltre che questa operazione non consente di aggiungere ulteriori funzionalità di mapping dei campi ai EF Core, ma solo di utilizzare i metodi lambda anziché richiedere sempre i metodi di stringa. Questa operazione è raramente utile poiché i campi sono raramente pubblici.

## <a name="preview-8"></a>Anteprima 8

### <a name="table-per-type-tpt-mapping"></a>Mapping tabella per tipo (TPT)

Per impostazione predefinita, EF Core esegue il mapping di una gerarchia di ereditarietà di tipi .NET a una singola tabella di database. Questa operazione è nota come mapping tabella per gerarchia (TPH). EF Core 5,0 consente inoltre di eseguire il mapping di ogni tipo .NET in una gerarchia di ereditarietà a una tabella di database differente. noto come mapping tabella per tipo (TPT).

Si consideri, ad esempio, questo modello con una gerarchia mappata:

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

Per impostazione predefinita, EF Core eseguirà il mapping a una singola tabella:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

Tuttavia, il mapping di ogni tipo di entità a una tabella diversa comporterà invece una tabella per tipo:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

Si noti che la creazione dei vincoli FOREIGN KEY mostrati in precedenza è stata aggiunta dopo aver diramato il codice per l'anteprima 8.

È possibile eseguire il mapping dei tipi di entità a tabelle diverse usando gli attributi di mapping:

```csharp
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

In alternativa `ModelBuilder` , usare la configurazione:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

La documentazione viene rilevata in base al problema [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).

### <a name="migrations-rebuild-sqlite-tables"></a>Migrazioni: ricompila tabelle SQLite

Rispetto ad altri database, SQLite è relativamente limitato alle funzionalità di manipolazione dello schema. Per eliminare una colonna da una tabella esistente, ad esempio, è necessario eliminare e ricreare l'intera tabella. EF Core 5,0 le migrazioni supportano ora la ricompilazione automatica della tabella per le modifiche dello schema che lo richiedono.

Si supponga, ad esempio, che sia stata `Unicorns` creata una tabella per un `Unicorn` tipo di entità:

```csharp
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

Si apprenderà quindi che l'archiviazione dell'età di un unicorno viene considerata molto scortese. Rimuovere quindi la proprietà, aggiungere una nuova migrazione e aggiornare il database. Questo aggiornamento avrà esito negativo quando si usa EF Core 3,1 perché la colonna non può essere eliminata. In EF Core 5,0 le migrazioni ricompilano invece la tabella:

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

Si noti che:

* Viene creata una tabella temporanea con lo schema desiderato per la nuova tabella
* I dati vengono copiati dalla tabella corrente nella tabella temporanea
* L'imposizione della chiave esterna è disattivata
* La tabella corrente è stata eliminata
* La tabella temporanea è stata rinominata come nuova tabella

La documentazione viene rilevata in base al problema [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).

### <a name="table-valued-functions"></a>Funzioni con valori di tabella

Questa funzionalità è stata fornita dalla community da [@pmiddleton](https://github.com/pmiddleton) . Molti ringraziamenti per il contributo.

EF Core 5,0 include supporto di prima classe per il mapping di metodi .NET a funzioni con valori di tabella (funzioni con valori). Queste funzioni possono quindi essere usate nelle query LINQ in cui anche la composizione aggiuntiva sui risultati della funzione verrà convertita in SQL.

Si consideri, ad esempio, questo TVF definito in un database SQL Server:

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

Il modello di EF Core richiede due tipi di entità per l'uso di TVF:

* `Employee`Tipo che esegue il mapping alla tabella Employees in modo normale
* `Report`Tipo che corrisponde alla forma restituita da TVF

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

Questi tipi devono essere inclusi nel modello di EF Core:

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

Si noti che `Report` non ha una chiave primaria e che pertanto deve essere configurata come tale.

Infine, è necessario eseguire il mapping di un metodo .NET a TVF nel database. Questo metodo può essere definito in DbContext usando il nuovo `FromExpression` Metodo:

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

Questo metodo usa un parametro e un tipo restituito che corrispondono ai TVF definiti in precedenza. Il metodo viene quindi aggiunto al modello di EF Core in OnModelCreating:

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

(L'uso di un'espressione lambda qui è un modo semplice per passare `MethodInfo` a EF core. Gli argomenti passati al metodo vengono ignorati.

È ora possibile scrivere query che chiamano `GetReports` e compongono i risultati. Esempio:

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

In SQL Server, viene convertito in:

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

Si noti che SQL è rooted nella `Employees` tabella, chiama `GetReports` e quindi aggiunge un'altra clausola WHERE ai risultati della funzione.

### <a name="flexible-queryupdate-mapping"></a>Mapping di query/aggiornamento flessibile

EF Core 5,0 consente il mapping dello stesso tipo di entità a oggetti di database diversi. Tali oggetti possono essere tabelle, viste o funzioni.

È ad esempio possibile eseguire il mapping di un tipo di entità sia a una vista di database che a una tabella di database:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

Per impostazione predefinita, EF Core effettuerà una query dalla vista e invierà gli aggiornamenti alla tabella. Ad esempio, eseguendo il codice seguente:

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

Restituisce una query sulla vista e quindi un aggiornamento alla tabella:

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>Configurazione della query Split a livello di contesto

Le query suddivise (vedere di seguito) possono ora essere configurate come impostazione predefinita per qualsiasi query eseguita dal DbContext. Questa configurazione è disponibile solo per i provider relazionali e pertanto deve essere specificata come parte della `UseProvider` configurazione. Esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="physicaladdress-mapping"></a>Mapping di PhysicalAddress

Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) . Molti ringraziamenti per il contributo.

La classe .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) standard viene ora mappata automaticamente a una colonna di tipo stringa per i database che non hanno già il supporto nativo. Per ulteriori informazioni, vedere gli esempi riportati di `IPAddress` seguito.

## <a name="preview-7"></a>Anteprima 7

### <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5,0 introduce `AddDbContextFactory` e `AddPooledDbContextFactory` per registrare una factory per la creazione di istanze di DbContext nel contenitore di inserimento delle dipendenze dell'applicazione (D.I.). Esempio:

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

I servizi delle applicazioni come ASP.NET Core controller possono quindi dipendere da `IDbContextFactory<TContext>` nel costruttore del servizio. Esempio:

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

Le istanze di DbContext possono quindi essere create e utilizzate in base alle esigenze. Esempio:

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

Si noti che le istanze di DbContext create in questo modo _non_ sono gestite dal provider di servizi dell'applicazione e pertanto devono essere eliminate dall'applicazione. Questa separazione è molto utile per le applicazioni blazer, in cui si consiglia l'uso di `IDbContextFactory` , ma può essere utile anche in altri scenari.

Le istanze di DbContext possono essere raggruppate chiamando `AddPooledDbContextFactory` . Questo pool funziona allo stesso modo di `AddDbContextPool` e presenta anche le stesse limitazioni.

La documentazione viene rilevata in base al problema [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).

### <a name="reset-dbcontext-state"></a>Reimposta stato DbContext

EF Core 5,0 introduce `ChangeTracker.Clear()` che cancella la DbContext di tutte le entità rilevate. Questa situazione in genere non è necessaria quando si usa la procedura consigliata per la creazione di una nuova istanza del contesto di breve durata per ogni unità di lavoro. Tuttavia, se è necessario reimpostare lo stato di un'istanza di DbContext, l'uso del nuovo `Clear()` metodo è più efficiente e affidabile rispetto alla disconnessione di massa di tutte le entità.

La documentazione viene rilevata in base al problema [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).

### <a name="new-pattern-for-store-generated-defaults"></a>Nuovo modello per le impostazioni predefinite generate dall'archivio

EF Core consente l'impostazione di un valore esplicito per una colonna che può anche avere un vincolo di valore predefinito. EF Core usa il valore predefinito CLR del tipo di proprietà Type come sentinella per questo oggetto; Se il valore non è il valore predefinito CLR, viene inserito; in caso contrario, viene utilizzato il valore predefinito del database.

In questo modo si creano problemi per i tipi in cui il valore predefinito di CLR non è una buona sentinella, in particolare `bool` Proprietà. EF Core 5,0 consente ora il campo sottostante come Nullable per i casi di questo tipo. Esempio:

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

Si noti che il campo sottostante ammette i valori null, ma la proprietà esposta pubblicamente non lo è. In questo modo, il valore Sentinel può essere `null` senza alcun effetto sulla superficie pubblica del tipo di entità. In questo caso, se `IsValid` non è mai impostato, verrà usato il valore predefinito del database poiché il campo sottostante rimane null. Se `true` o `false` sono impostati, questo valore viene salvato in modo esplicito nel database.

La documentazione viene rilevata in base al problema [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).

### <a name="cosmos-partition-keys"></a>Chiavi di partizione Cosmos

EF Core consente di includere la chiave di partizione Cosmos nel modello EF. Esempio:

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

A partire dalla versione Preview 7, la chiave di partizione è inclusa nel PK del tipo di entità e viene usata per migliorare le prestazioni in alcune query.

La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="cosmos-configuration"></a>Configurazione di Cosmos

EF Core 5,0 migliora la configurazione delle connessioni Cosmos e Cosmos.

In precedenza EF Core necessario specificare in modo esplicito l'endpoint e la chiave per la connessione a un database Cosmos. EF Core 5,0 consente invece di usare una stringa di connessione. Inoltre, EF Core 5,0 consente l'impostazione esplicita dell'istanza WebProxy. Esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

È ora possibile configurare anche molti altri valori di timeout, limiti e così via. Esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

Infine, la modalità di connessione predefinita è ora `ConnectionMode.Gateway` , che è in genere più compatibile.

La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="scaffold-dbcontext-now-singularizes"></a>Scaffold-DbContext ora singolari

In precedenza, durante l'impalcatura di un DbContext da un database esistente, EF Core creerà nomi di tipi di entità che corrispondono ai nomi di tabella nel database. Le tabelle, ad esempio, hanno `People` `Addresses` generato i tipi di entità denominati `People` e `Addresses` .

Nelle versioni precedenti questo comportamento era configurabile tramite la registrazione di un servizio di pluralismo. Ora, in EF Core 5,0, il pacchetto [umanizzator](https://www.nuget.org/packages/Humanizer.Core/) viene usato come servizio di pluralismo predefinito. Ciò significa `People` che le tabelle e `Addresses` verranno ora decodificate in tipi di entità denominati `Person` e `Address` .

### <a name="savepoints"></a>Salvataggio

EF Core supporta ora [salvataggio](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) per un maggiore controllo sulle transazioni che eseguono più operazioni.

Salvataggio può essere creato, rilasciato ed eseguito manualmente il rollback. Esempio:

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

Inoltre, EF Core ora verrà eseguito il rollback all'ultimo salvataggio quando l'esecuzione `SaveChanges` non riesce. Questo consente di ritentare SaveChanges senza ritentare l'intera transazione.

La documentazione viene rilevata in base al problema [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).

## <a name="preview-6"></a>Preview 6

### <a name="split-queries-for-related-collections"></a>Suddividere le query per le raccolte correlate

A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ. In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso. Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.

EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL. Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query. È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.

#### <a name="split-queries-with-include"></a>Suddividere le query con Includi

Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

La nuova `AsSplitQuery` API può essere usata per modificare questo comportamento. Esempio:

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery è disponibile per tutti i provider di database relazionali e può essere usato in qualsiasi punto della query, proprio come AsNoTracking. EF Core genera ora le tre query SQL seguenti:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

Tutte le operazioni nella radice della query sono supportate. Sono incluse le operazioni OrderBy/Skip/Take, le operazioni di join, FirstOrDefault e le operazioni di selezione di un singolo risultato analogo.

Si noti che le inclusioni filtrate con OrderBy/Skip/Take non sono supportate nella versione Preview 6, ma sono disponibili nelle compilazioni giornaliere e saranno incluse nella versione Preview 7.

#### <a name="split-queries-with-collection-projections"></a>Suddividere query con proiezioni di raccolta

`AsSplitQuery` può essere usato anche quando le raccolte vengono caricate nelle proiezioni. Esempio:

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Questa query LINQ genera le due query SQL seguenti quando si usa il provider SQLite:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Si noti che è supportata solo la materializzazione della raccolta. Qualsiasi composizione dopo `e.Albums` il caso precedente non comporterà una query Split. I miglioramenti apportati a questa area vengono rilevati da [#21234](https://github.com/dotnet/efcore/issues/21234).

### <a name="indexattribute"></a>IndexAttribute

Il nuovo IndexAttribute può essere inserito in un tipo di entità per specificare un indice per una singola colonna. Esempio:

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

Per SQL Server, le migrazioni genereranno la seguente SQL:

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute può essere usato anche per specificare un indice che si estende su più colonne. Esempio:

```csharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

Per SQL Server, il risultato è il seguente:

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="improved-query-translation-exceptions"></a>Eccezioni di conversione query migliorate

Si sta continuando a migliorare i messaggi di eccezione generati quando la conversione di query non riesce. Questa query, ad esempio, usa la proprietà non mappata `IsSigned` :

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core genererà l'eccezione seguente indicante che la conversione `IsSigned` non è riuscita perché non è stato eseguito il mapping:

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

Analogamente, vengono generati messaggi di eccezione migliori quando si tenta di tradurre i confronti di stringhe con la semantica dipendente dalle impostazioni cultura. Questa query, ad esempio, tenta di usare `StringComparison.CurrentCulture` :

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core ora verrà generata l'eccezione seguente:

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a>Specifica ID transazione

Questa funzionalità è stata fornita dalla community da [@Marusyk](https://github.com/Marusyk) . Molti ringraziamenti per il contributo.

EF Core espone un ID transazione per la correlazione delle transazioni tra le chiamate. Questo ID viene in genere impostato da EF Core quando viene avviata una transazione. Se invece l'applicazione avvia la transazione, questa funzionalità consente all'applicazione di impostare in modo esplicito l'ID della transazione in modo che venga correlata correttamente ovunque venga utilizzata. Esempio:

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Mapping IPAddress

Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) . Molti ringraziamenti per il contributo.

La [classe IPAddress](/dotnet/api/system.net.ipaddress) .NET standard viene ora mappata automaticamente a una colonna stringa per i database che non dispongono già del supporto nativo. Ad esempio, si consiglia di eseguire il mapping di questo tipo di entità:

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

In SQL Server, le migrazioni comporteranno la creazione della tabella seguente:

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

Le entità possono quindi essere aggiunte in modo normale:

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

E il SQL risultante inserirà l'indirizzo IPv4 o IPv6 normalizzato:

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Escludi onconfigurazione durante l'impalcatura

Quando un DbContext viene sottoposto a impalcatura da un database esistente, per impostazione predefinita EF core crea un overload configurante con una stringa di connessione in modo che il contesto sia immediatamente utilizzabile. Questa operazione non è tuttavia utile se si dispone già di una classe parziale con Configuring oppure se si sta configurando il contesto in altro modo.

Per risolvere questo problema, è ora possibile impostare i comandi di impalcatura per omettere la generazione di onconfigurazione. Esempio:

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

In alternativa, nella console di gestione pacchetti:

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

Si consiglia di usare [una stringa di connessione denominata e una risorsa di archiviazione sicura, ad esempio i segreti utente](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Traduzioni per FirstOrDefault in stringhe

Questa funzionalità è stata fornita dalla community da [@dvoreckyaa](https://github.com/dvoreckyaa) . Molti ringraziamenti per il contributo.

FirstOrDefault e operatori simili per i caratteri nelle stringhe sono ora tradotti. Ad esempio, questa query LINQ:

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Verrà convertito nel codice SQL seguente quando si usa SQL Server:

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Semplificare i blocchi di case

EF Core genera ora query migliori con blocchi di maiuscole e minuscole. Ad esempio, questa query LINQ:

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Was on SQL Server formalmente convertita in:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

Ma ora viene convertito in:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>Regole di confronto di database

È ora possibile specificare le regole di confronto predefinite per un database nel modello EF. Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database. Esempio:

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Migrations genera quindi quanto segue per creare il database in SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche. Esempio:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.

Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse. Esempio:

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

Verrà generata la query seguente per SQL Server:

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Si noti che le regole di confronto ad hoc devono essere utilizzate con cautela, in quanto possono influire negativamente sulle prestazioni del database.

La documentazione viene rilevata in base al problema [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Argomenti del flusso in IDesignTimeDbContextFactory

Gli argomenti vengono ora propagati dalla riga di comando nel `CreateDbContext` metodo di [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1). Per indicare, ad esempio, che si tratta di una build di sviluppo, è possibile passare un argomento personalizzato, ad esempio, `dev` nella riga di comando:

```console
dotnet ef migrations add two --verbose --dev
```

Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto. Esempio:

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).

### <a name="no-tracking-queries-with-identity-resolution"></a>Query senza rilevamento con risoluzione di identità

È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità. La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria.

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento. Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata. Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).

### <a name="stored-persisted-computed-columns"></a>Colonne calcolate (rese permanente) archiviate

La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo. Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore. Consente inoltre di indicizzare la colonna per alcuni database.

EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate. Esempio:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>Colonne calcolate SQLite

EF Core supporta ora le colonne calcolate nei database SQLite.

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>Configurare la precisione del database/ridimensionare il modello

È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli. Esempio:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)".

La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).

### <a name="specify-sql-server-index-fill-factor"></a>Specificare il fattore di riempimento dell'indice SQL Server

È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server. Esempio:

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Inclusione filtrato

Il metodo include ora supporta il filtraggio delle entità incluse. Esempio:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".

È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse. Esempio:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.

Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nuova API ModelBuilder per le proprietà di navigazione

Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships). Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva. Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Si noti che l' `Navigation` API non sostituisce la configurazione della relazione. Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.

Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione

Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando. Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi:

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Per informazioni dettagliate, vedere la documentazione sulle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e la [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .

---
Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:

```dotnetcli
dotnet ef database update --connection "connection string"
```

Per informazioni dettagliate, vedere la [documentazione degli strumenti](xref:core/cli/dotnet#dotnet-ef-database-update) .

Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors ha restituito

Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database. Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.

`EnableDetailedErrors`Se si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.

Esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).

### <a name="cosmos-partition-keys"></a>Chiavi di partizione Cosmos

La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query. Esempio:

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Supporto per la funzione SQL Server DataLength

È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo. Esempio:

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Usare un attributo C# per specificare un campo di supporto delle proprietà

È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà. Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente. Esempio:

```csharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).

### <a name="complete-discriminator-mapping"></a>Mapping completo del discriminatore

EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](xref:core/modeling/inheritance). Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore. EF Core 5,0 implementa ora questi miglioramenti.

Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Si tratta del comportamento predefinito che inizia con Preview 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Miglioramenti delle prestazioni in Microsoft. Data. sqlite

Sono stati apportati due miglioramenti alle prestazioni per SQLIte:

* Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.
* L'inizializzazione di SqliteConnection è ora Lazy.

Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>Registrazione semplice

Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6. Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.

La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).

### <a name="simple-way-to-get-generated-sql"></a>Modo semplice per ottenere SQL generato

EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Usare un attributo C# per indicare che un'entità non ha una chiave

È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` . Esempio:

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>La stringa di connessione o di connessione può essere modificata in DbContext inizializzato

È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione. Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto. Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.

La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxy di rilevamento delle modifiche

EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) e [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche. Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.

La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Viste di debug migliorate

Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi. Una visualizzazione di debug per il modello è stata implementata qualche tempo fa. Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).

### <a name="improved-handling-of-database-null-semantics"></a>Gestione migliorata della semantica null del database

I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL. Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null. EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#. EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.

La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Proprietà indicizzatore

EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#. Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.

La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generazione di vincoli check per i mapping enum

EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione. Esempio:

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>Relazionale

`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` . Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale. Esempio:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Concorrenza ottimistica di Cosmos con ETag

Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag. Usare il generatore di modelli in OnModelCreating per configurare un ETag:

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges genera quindi un'operazione `DbUpdateConcurrencyException` in un conflitto di concorrenza, che [può essere gestita](xref:core/saving/concurrency) per implementare nuovi tentativi e così via.

La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).

### <a name="query-translations-for-more-datetime-constructs"></a>Eseguire query sulle traduzioni per più costrutti DateTime

Vengono ora convertite le query contenenti nuove costruzioni DateTime.

Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:

* DateDiffWeek
* DateFromParts

Esempio:

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translations-for-more-byte-array-constructs"></a>Eseguire query sulle traduzioni per altri costrutti di matrici di byte

Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.

La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-reverse"></a>Conversione di query per invertire

Le query che utilizzano `Reverse` ora vengono convertite. Esempio:

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-bitwise-operators"></a>Conversione di query per operatori bit per bit

Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-strings-on-cosmos"></a>Conversione di query per le stringhe in Cosmos

Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).
