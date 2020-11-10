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
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="30df1-103">Novità di EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="30df1-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="30df1-104">Tutte le funzionalità pianificate per EF Core 5,0 sono state completate.</span><span class="sxs-lookup"><span data-stu-id="30df1-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="30df1-105">Questa pagina contiene una panoramica delle modifiche interessanti introdotte in ogni anteprima.</span><span class="sxs-lookup"><span data-stu-id="30df1-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="30df1-106">Questa pagina non duplica il [piano per EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="30df1-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="30df1-107">Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.</span><span class="sxs-lookup"><span data-stu-id="30df1-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="30df1-108">RC1</span><span class="sxs-lookup"><span data-stu-id="30df1-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="30df1-109">Molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="30df1-109">Many-to-many</span></span>

<span data-ttu-id="30df1-110">EF Core 5,0 supporta relazioni molti-a-molti senza eseguire in modo esplicito il mapping della tabella di join.</span><span class="sxs-lookup"><span data-stu-id="30df1-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="30df1-111">Si considerino, ad esempio, i tipi di entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="30df1-111">For example, consider these entity types:</span></span>

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

<span data-ttu-id="30df1-112">Si noti che `Post` contiene una raccolta di `Tags` e `Tag` contiene una raccolta di `Posts` .</span><span class="sxs-lookup"><span data-stu-id="30df1-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="30df1-113">EF Core 5,0 riconosce questa relazione come una relazione molti-a-molti per convenzione.</span><span class="sxs-lookup"><span data-stu-id="30df1-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="30df1-114">Questo significa che non è necessario alcun codice in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="30df1-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

<span data-ttu-id="30df1-115">Quando `EnsureCreated` si utilizzano le migrazioni (o) per creare il database, EF Core creerà automaticamente la tabella di join.</span><span class="sxs-lookup"><span data-stu-id="30df1-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="30df1-116">Ad esempio, in SQL Server per questo modello, EF Core genera:</span><span class="sxs-lookup"><span data-stu-id="30df1-116">For example, on SQL Server for this model, EF Core generates:</span></span>

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

<span data-ttu-id="30df1-117">La creazione e l'associazione di `Tag` `Post` entità e determinano la creazione automatica di aggiornamenti della tabella di join.</span><span class="sxs-lookup"><span data-stu-id="30df1-117">Creating and associating `Tag` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="30df1-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-118">For example:</span></span>

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

<span data-ttu-id="30df1-119">Dopo aver inserito i post e i tag, EF creerà automaticamente le righe nella tabella di join.</span><span class="sxs-lookup"><span data-stu-id="30df1-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="30df1-120">Ad esempio, in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="30df1-120">For example, on SQL Server:</span></span>

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

<span data-ttu-id="30df1-121">Per le query, includere e altre operazioni di query funzionano esattamente come per qualsiasi altra relazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="30df1-122">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-122">For example:</span></span>

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

<span data-ttu-id="30df1-123">Il SQL generato usa la tabella di join automaticamente per ripristinare tutti i tag correlati:</span><span class="sxs-lookup"><span data-stu-id="30df1-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

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

<span data-ttu-id="30df1-124">A differenza di EF6, EF Core consente la personalizzazione completa della tabella di join.</span><span class="sxs-lookup"><span data-stu-id="30df1-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="30df1-125">Nel codice seguente, ad esempio, viene configurata una relazione molti-a-molti che include anche le navigazioni per l'entità di join e in cui l'entità join contiene una proprietà payload:</span><span class="sxs-lookup"><span data-stu-id="30df1-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

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
> <span data-ttu-id="30df1-126">Il supporto per l'impalcatura di relazioni molti-a-molti dal database non è ancora stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="30df1-126">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="30df1-127">Vedere la pagina relativa al [rilevamento del problema](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="30df1-127">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="30df1-128">Eseguire il mapping di tipi di entità alle query</span><span class="sxs-lookup"><span data-stu-id="30df1-128">Map entity types to queries</span></span>

<span data-ttu-id="30df1-129">I tipi di entità vengono in genere mappati a tabelle o viste in modo che EF Core effettuerà il pull del contenuto della tabella o della vista durante l'esecuzione di query per quel tipo.</span><span class="sxs-lookup"><span data-stu-id="30df1-129">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="30df1-130">EF Core 5,0 consente il mapping di un tipo di entità a una "query di definizione".</span><span class="sxs-lookup"><span data-stu-id="30df1-130">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="30df1-131">(Questo è stato parzialmente supportato nelle versioni precedenti, ma è molto migliorato e presenta una sintassi diversa in EF Core 5,0).</span><span class="sxs-lookup"><span data-stu-id="30df1-131">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="30df1-132">Si considerino, ad esempio, due tabelle. una con post moderni; l'altro con i post legacy.</span><span class="sxs-lookup"><span data-stu-id="30df1-132">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="30df1-133">La tabella Posts moderna include alcune colonne aggiuntive, ma ai fini dell'applicazione si desidera che sia i post moderni che quelli legacy vengano combinati e mappati a un tipo di entità con tutte le proprietà necessarie:</span><span class="sxs-lookup"><span data-stu-id="30df1-133">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

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

<span data-ttu-id="30df1-134">In EF Core 5,0, `ToSqlQuery` può essere utilizzato per eseguire il mapping di questo tipo di entità a una query che esegue il pull e combina le righe da entrambe le tabelle:</span><span class="sxs-lookup"><span data-stu-id="30df1-134">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="30df1-135">Si noti che la `legacy_posts` tabella non contiene una `Category` colonna, pertanto viene invece sintetizzato un valore predefinito per tutti i post legacy.</span><span class="sxs-lookup"><span data-stu-id="30df1-135">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="30df1-136">Questo tipo di entità può quindi essere utilizzato in modo normale per le query LINQ.</span><span class="sxs-lookup"><span data-stu-id="30df1-136">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="30df1-137">Ad esempio,</span><span class="sxs-lookup"><span data-stu-id="30df1-137">For example.</span></span> <span data-ttu-id="30df1-138">query LINQ:</span><span class="sxs-lookup"><span data-stu-id="30df1-138">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="30df1-139">Genera il codice SQL seguente in SQLite:</span><span class="sxs-lookup"><span data-stu-id="30df1-139">Generates the following SQL on SQLite:</span></span>

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

<span data-ttu-id="30df1-140">Si noti che la query configurata per il tipo di entità viene utilizzata come avvio per la composizione della query LINQ completa.</span><span class="sxs-lookup"><span data-stu-id="30df1-140">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="30df1-141">Contatori di eventi</span><span class="sxs-lookup"><span data-stu-id="30df1-141">Event counters</span></span>

<span data-ttu-id="30df1-142">I [contatori di eventi .NET](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) rappresentano un modo per esporre in modo efficiente le metriche delle prestazioni da un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-142">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="30df1-143">EF Core 5,0 include i contatori di eventi nella `Microsoft.EntityFrameworkCore` categoria.</span><span class="sxs-lookup"><span data-stu-id="30df1-143">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="30df1-144">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-144">For example:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="30df1-145">Ciò indica ai contatori DotNet di iniziare a raccogliere gli eventi di EF Core per il processo 49496.</span><span class="sxs-lookup"><span data-stu-id="30df1-145">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="30df1-146">Verrà generato un output simile al seguente nella console:</span><span class="sxs-lookup"><span data-stu-id="30df1-146">This generates output like this in the console:</span></span>

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

### <a name="property-bags"></a><span data-ttu-id="30df1-147">Contenitori delle proprietà</span><span class="sxs-lookup"><span data-stu-id="30df1-147">Property bags</span></span>

<span data-ttu-id="30df1-148">EF Core 5,0 consente di eseguire il mapping dello stesso tipo CLR a più tipi di entità diversi.</span><span class="sxs-lookup"><span data-stu-id="30df1-148">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="30df1-149">Tali tipi sono noti come tipi di entità di tipo condiviso.</span><span class="sxs-lookup"><span data-stu-id="30df1-149">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="30df1-150">Questa funzionalità combinata con le proprietà dell'indicizzatore (inclusa nella versione Preview 1) consente di usare i contenitori di proprietà come tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="30df1-150">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="30df1-151">Il DbContext seguente, ad esempio, configura il tipo BCL `Dictionary<string, object>` come tipo di entità di tipo condiviso per i prodotti e le categorie.</span><span class="sxs-lookup"><span data-stu-id="30df1-151">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

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

<span data-ttu-id="30df1-152">Gli oggetti dizionario ("contenitori di proprietà") possono ora essere aggiunti al contesto come istanze di entità e salvati.</span><span class="sxs-lookup"><span data-stu-id="30df1-152">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="30df1-153">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-153">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="30df1-154">Queste entità possono quindi essere sottoposte a query e aggiornate in modo normale:</span><span class="sxs-lookup"><span data-stu-id="30df1-154">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="30df1-155">Intercettazione ed eventi di SaveChanges</span><span class="sxs-lookup"><span data-stu-id="30df1-155">SaveChanges interception and events</span></span>

<span data-ttu-id="30df1-156">EF Core 5,0 introduce sia gli eventi .NET che un intercettore EF Core attivato quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="30df1-156">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="30df1-157">Gli eventi sono semplici da usare. Per esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-157">The events are simple to use; for example:</span></span>

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

<span data-ttu-id="30df1-158">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="30df1-158">Notice that:</span></span>

* <span data-ttu-id="30df1-159">Il mittente dell'evento è l' `DbContext` istanza</span><span class="sxs-lookup"><span data-stu-id="30df1-159">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="30df1-160">Gli argomenti per l' `SavedChanges` evento contengono il numero di entità salvate nel database</span><span class="sxs-lookup"><span data-stu-id="30df1-160">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="30df1-161">L'intercettore è definito da `ISaveChangesInterceptor` , ma spesso è conveniente da ereditare da `SaveChangesInterceptor` per evitare l'implementazione di ogni metodo.</span><span class="sxs-lookup"><span data-stu-id="30df1-161">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convienient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="30df1-162">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-162">For example:</span></span>

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

<span data-ttu-id="30df1-163">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="30df1-163">Notice that:</span></span>

* <span data-ttu-id="30df1-164">L'intercettore dispone di metodi Sync e Async.</span><span class="sxs-lookup"><span data-stu-id="30df1-164">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="30df1-165">Questa operazione può essere utile se è necessario eseguire operazioni di I/O asincrone, ad esempio la scrittura in un server di controllo.</span><span class="sxs-lookup"><span data-stu-id="30df1-165">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="30df1-166">L'intercettore consente di ignorare SaveChanges utilizzando il `InterceptionResult` meccanismo comune a tutti gli intercettori.</span><span class="sxs-lookup"><span data-stu-id="30df1-166">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="30df1-167">Il lato negativo degli intercettori è che devono essere registrati nel DbContext quando viene costruito.</span><span class="sxs-lookup"><span data-stu-id="30df1-167">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="30df1-168">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-168">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="30df1-169">Al contrario, gli eventi possono essere registrati nell'istanza di DbContext in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="30df1-169">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="30df1-170">Escludi tabelle dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="30df1-170">Exclude tables from migrations</span></span>

<span data-ttu-id="30df1-171">A volte è utile avere un solo tipo di entità mappato in più oggetti DbContext.</span><span class="sxs-lookup"><span data-stu-id="30df1-171">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="30df1-172">Ciò vale soprattutto quando si usano i [contesti delimitati](https://www.martinfowler.com/bliki/BoundedContext.html), per i quali è comune avere un tipo DbContext diverso per ogni contesto delimitato.</span><span class="sxs-lookup"><span data-stu-id="30df1-172">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="30df1-173">Un tipo, ad esempio, `User` può essere necessario sia per un contesto di autorizzazione che per un contesto di Reporting.</span><span class="sxs-lookup"><span data-stu-id="30df1-173">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="30df1-174">Se viene apportata una modifica al `User` tipo, le migrazioni per entrambi oggetti DbContext tenterà di aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="30df1-174">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="30df1-175">Per evitare questo problema, è possibile configurare il modello per uno dei contesti in modo da escludere la tabella dalle relative migrazioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-175">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="30df1-176">Nel codice riportato di seguito, il genererà le `AuthorizationContext` migrazioni per le modifiche apportate alla `Users` tabella, ma `ReportingContext` non ne impedirà lo scontro tra le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-176">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

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

### <a name="required-11-dependents"></a><span data-ttu-id="30df1-177">Dipendenti 1:1 richiesti</span><span class="sxs-lookup"><span data-stu-id="30df1-177">Required 1:1 dependents</span></span>

<span data-ttu-id="30df1-178">In EF Core 3,1, l'entità finale dipendente di una relazione uno-a-uno è sempre considerata facoltativa.</span><span class="sxs-lookup"><span data-stu-id="30df1-178">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="30df1-179">Questo è stato più evidente quando si usano entità di proprietà.</span><span class="sxs-lookup"><span data-stu-id="30df1-179">This was most apparent when using owned entities.</span></span> <span data-ttu-id="30df1-180">Si consideri, ad esempio, il modello e la configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="30df1-180">For example, consider the following model and configuration:</span></span>

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

<span data-ttu-id="30df1-181">Ciò comporta la creazione della migrazione della tabella seguente per SQLite:</span><span class="sxs-lookup"><span data-stu-id="30df1-181">This results in Migrations creating the following table for SQLite:</span></span>

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

<span data-ttu-id="30df1-182">Si noti che tutte le colonne ammettono valori null, anche se alcune delle `HomeAddress` proprietà sono state configurate in modo obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="30df1-182">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="30df1-183">Inoltre, quando si esegue una query per un oggetto `Person` , se tutte le colonne per l'indirizzo di casa o di lavoro sono null, EF Core lascerà le `HomeAddress` proprietà e/o `WorkAddress` come null, anziché impostare un'istanza vuota di `Address` .</span><span class="sxs-lookup"><span data-stu-id="30df1-183">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="30df1-184">In EF Core 5,0, la `HomeAddress` navigazione può ora essere configurata come dipendente obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="30df1-184">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="30df1-185">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-185">For example:</span></span>

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

<span data-ttu-id="30df1-186">Nella tabella creata dalle migrazioni verranno ora incluse colonne che non ammettono i valori null per le proprietà obbligatorie del dipendente richiesto:</span><span class="sxs-lookup"><span data-stu-id="30df1-186">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

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

<span data-ttu-id="30df1-187">Inoltre, EF Core ora genererà un'eccezione se viene effettuato un tentativo di salvare un proprietario con una dipendenza obbligatoria da null.</span><span class="sxs-lookup"><span data-stu-id="30df1-187">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="30df1-188">In questo esempio EF Core genererà un'eccezione quando si tenta di salvare un oggetto `Person` con un valore null `HomeAddress` .</span><span class="sxs-lookup"><span data-stu-id="30df1-188">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="30df1-189">Infine, EF Core creerà comunque un'istanza di un oggetto dipendente richiesto anche quando tutte le colonne per il dipendente richiesto hanno valori null.</span><span class="sxs-lookup"><span data-stu-id="30df1-189">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="30df1-190">Opzioni per la generazione della migrazione</span><span class="sxs-lookup"><span data-stu-id="30df1-190">Options for migration generation</span></span>

<span data-ttu-id="30df1-191">EF Core 5,0 introduce un maggiore controllo sulla generazione delle migrazioni per scopi diversi.</span><span class="sxs-lookup"><span data-stu-id="30df1-191">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="30df1-192">con la capacità di:</span><span class="sxs-lookup"><span data-stu-id="30df1-192">This includes the ability to:</span></span>

* <span data-ttu-id="30df1-193">Verificare se la migrazione viene generata per uno script o per l'esecuzione immediata</span><span class="sxs-lookup"><span data-stu-id="30df1-193">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="30df1-194">Verificare se è in corso la generazione di uno script idempotente</span><span class="sxs-lookup"><span data-stu-id="30df1-194">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="30df1-195">Sapere se lo script deve escludere le istruzioni Transaction (vedere gli _script di migrazione con le transazioni_ seguenti).</span><span class="sxs-lookup"><span data-stu-id="30df1-195">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="30df1-196">Questo comportamento viene specificato da un' `MigrationsSqlGenerationOptions` enumerazione, che ora può essere passata a `IMigrator.GenerateScript` .</span><span class="sxs-lookup"><span data-stu-id="30df1-196">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="30df1-197">In questo lavoro è anche inclusa una migliore generazione di script idempotente con chiamate a `EXEC` su SQL Server quando necessario.</span><span class="sxs-lookup"><span data-stu-id="30df1-197">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="30df1-198">Questo lavoro consente anche miglioramenti simili per gli script generati da altri provider di database, incluso PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="30df1-198">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="30df1-199">Script di migrazione con transazioni</span><span class="sxs-lookup"><span data-stu-id="30df1-199">Migrations scripts with transactions</span></span>

<span data-ttu-id="30df1-200">Gli script SQL generati dalle migrazioni ora contengono istruzioni per avviare ed eseguire il commit delle transazioni nel modo appropriato per la migrazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-200">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="30df1-201">Ad esempio, lo script di migrazione seguente è stato generato da due migrazioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-201">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="30df1-202">Si noti che ogni migrazione è ora applicata all'interno di una transazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-202">Notice that each migration is now applied inside a transaction.</span></span>

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

<span data-ttu-id="30df1-203">Come indicato nella sezione precedente, l'utilizzo delle transazioni può essere disabilitato se le transazioni devono essere gestite in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="30df1-203">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="30df1-204">Vedere migrazioni in sospeso</span><span class="sxs-lookup"><span data-stu-id="30df1-204">See pending migrations</span></span>

<span data-ttu-id="30df1-205">Questa funzionalità è stata fornita dalla community da [@Psypher9](https://github.com/Psypher9) .</span><span class="sxs-lookup"><span data-stu-id="30df1-205">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="30df1-206">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-206">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-207">Il `dotnet ef migrations list` comando ora Mostra le migrazioni non ancora applicate al database.</span><span class="sxs-lookup"><span data-stu-id="30df1-207">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="30df1-208">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-208">For example:</span></span>

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="30df1-209">Inoltre, è ora disponibile un `Get-Migration` comando per la console di gestione pacchetti con la stessa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="30df1-209">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="30df1-210">API ModelBuilder per gli operatori di confronto dei valori</span><span class="sxs-lookup"><span data-stu-id="30df1-210">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="30df1-211">EF Core proprietà per i tipi modificabili personalizzati [richiedono un operatore di confronto del valore](xref:core/modeling/value-comparers) per rilevare correttamente le modifiche delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="30df1-211">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="30df1-212">Questa ora può essere specificata come parte della configurazione della conversione del valore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="30df1-212">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="30df1-213">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-213">For example:</span></span>

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

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="30df1-214">Metodi di EntityEntry TryGetValue</span><span class="sxs-lookup"><span data-stu-id="30df1-214">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="30df1-215">Questa funzionalità è stata fornita dalla community da [@m4ss1m0g](https://github.com/m4ss1m0g) .</span><span class="sxs-lookup"><span data-stu-id="30df1-215">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="30df1-216">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-216">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-217">`TryGetValue`È stato aggiunto un metodo a `EntityEntry.CurrentValues` e `EntityEntry.OriginalValues` .</span><span class="sxs-lookup"><span data-stu-id="30df1-217">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="30df1-218">Ciò consente di richiedere il valore di una proprietà senza prima verificare se è stato eseguito il mapping della proprietà nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="30df1-218">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="30df1-219">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-219">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="30df1-220">Dimensioni massime batch predefinite per SQL Server</span><span class="sxs-lookup"><span data-stu-id="30df1-220">Default max batch size for SQL Server</span></span>

<span data-ttu-id="30df1-221">A partire da EF Core 5,0, le dimensioni massime del batch predefinite per SaveChanges in SQL Server ora sono 42.</span><span class="sxs-lookup"><span data-stu-id="30df1-221">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="30df1-222">Come è noto, si tratta anche della risposta alla domanda finale della vita, dell'universo e di tutto.</span><span class="sxs-lookup"><span data-stu-id="30df1-222">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="30df1-223">Tuttavia, si tratta probabilmente di una coincidenza, poiché il valore è stato ottenuto tramite l' [analisi delle prestazioni in batch](https://github.com/dotnet/efcore/issues/9270).</span><span class="sxs-lookup"><span data-stu-id="30df1-223">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="30df1-224">Non si ritiene che sia stata individuata una forma di domanda definitiva, anche se è piuttosto plausibile che la terra sia stata creata per comprendere il motivo per cui SQL Server funziona in modo corretto.</span><span class="sxs-lookup"><span data-stu-id="30df1-224">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="30df1-225">Ambiente predefinito per lo sviluppo</span><span class="sxs-lookup"><span data-stu-id="30df1-225">Default environment to Development</span></span>

<span data-ttu-id="30df1-226">Gli strumenti da riga di comando EF Core ora configurano automaticamente le `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` variabili di ambiente e in "Development".</span><span class="sxs-lookup"><span data-stu-id="30df1-226">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="30df1-227">In questo modo, quando si usa l'host generico in linea con l'esperienza ASP.NET Core durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="30df1-227">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="30df1-228">Vedere [#19903](https://github.com/dotnet/efcore/issues/19903).</span><span class="sxs-lookup"><span data-stu-id="30df1-228">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="30df1-229">Ordinamento delle colonne per migrazioni migliori</span><span class="sxs-lookup"><span data-stu-id="30df1-229">Better migrations column ordering</span></span>

<span data-ttu-id="30df1-230">Le colonne per le classi di base senza mapping sono ora ordinate dopo altre colonne per i tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="30df1-230">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="30df1-231">Si noti che questo influisca solo sulle tabelle appena create.</span><span class="sxs-lookup"><span data-stu-id="30df1-231">Note this only impacts newly created tables.</span></span> <span data-ttu-id="30df1-232">L'ordine delle colonne per le tabelle esistenti rimane invariato.</span><span class="sxs-lookup"><span data-stu-id="30df1-232">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="30df1-233">Vedere [#11314](https://github.com/dotnet/efcore/issues/11314).</span><span class="sxs-lookup"><span data-stu-id="30df1-233">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="30df1-234">Miglioramenti delle query</span><span class="sxs-lookup"><span data-stu-id="30df1-234">Query improvements</span></span>

<span data-ttu-id="30df1-235">EF Core 5,0 RC1 contiene alcuni miglioramenti aggiuntivi per la conversione delle query:</span><span class="sxs-lookup"><span data-stu-id="30df1-235">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="30df1-236">Conversione di `is` in Cosmos. vedere [#16391](https://github.com/dotnet/efcore/issues/16391)</span><span class="sxs-lookup"><span data-stu-id="30df1-236">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="30df1-237">È ora possibile annotare le funzioni mappate dall'utente per controllare la propagazione Null. vedere [#19609](https://github.com/dotnet/efcore/issues/19609)</span><span class="sxs-lookup"><span data-stu-id="30df1-237">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="30df1-238">Supporto per la conversione di GroupBy con aggregazioni condizionali. vedere [#11711](https://github.com/dotnet/efcore/issues/11711)</span><span class="sxs-lookup"><span data-stu-id="30df1-238">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="30df1-239">Traduzione dell'operatore DISTINCT sull'elemento Group prima dell'aggregazione. vedere [#17376](https://github.com/dotnet/efcore/issues/17376)</span><span class="sxs-lookup"><span data-stu-id="30df1-239">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="30df1-240">Creazione di modelli per i campi</span><span class="sxs-lookup"><span data-stu-id="30df1-240">Model building for fields</span></span>

<span data-ttu-id="30df1-241">Infine, per RC1, EF Core ora consente l'uso dei metodi lambda in ModelBuilder per i campi e le proprietà.</span><span class="sxs-lookup"><span data-stu-id="30df1-241">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="30df1-242">Se, ad esempio, si è contrari alle proprietà per qualche motivo e si decide di usare campi pubblici, è ora possibile eseguire il mapping di questi campi usando i generatori di espressioni lambda:</span><span class="sxs-lookup"><span data-stu-id="30df1-242">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

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

<span data-ttu-id="30df1-243">Sebbene questa operazione sia ora possibile, non è consigliabile eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-243">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="30df1-244">Si noti inoltre che questa operazione non consente di aggiungere ulteriori funzionalità di mapping dei campi ai EF Core, ma solo di utilizzare i metodi lambda anziché richiedere sempre i metodi di stringa.</span><span class="sxs-lookup"><span data-stu-id="30df1-244">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="30df1-245">Questa operazione è raramente utile poiché i campi sono raramente pubblici.</span><span class="sxs-lookup"><span data-stu-id="30df1-245">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="30df1-246">Anteprima 8</span><span class="sxs-lookup"><span data-stu-id="30df1-246">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="30df1-247">Mapping tabella per tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="30df1-247">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="30df1-248">Per impostazione predefinita, EF Core esegue il mapping di una gerarchia di ereditarietà di tipi .NET a una singola tabella di database.</span><span class="sxs-lookup"><span data-stu-id="30df1-248">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="30df1-249">Questa operazione è nota come mapping tabella per gerarchia (TPH).</span><span class="sxs-lookup"><span data-stu-id="30df1-249">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="30df1-250">EF Core 5,0 consente inoltre di eseguire il mapping di ogni tipo .NET in una gerarchia di ereditarietà a una tabella di database differente. noto come mapping tabella per tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="30df1-250">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="30df1-251">Si consideri, ad esempio, questo modello con una gerarchia mappata:</span><span class="sxs-lookup"><span data-stu-id="30df1-251">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="30df1-252">Per impostazione predefinita, EF Core eseguirà il mapping a una singola tabella:</span><span class="sxs-lookup"><span data-stu-id="30df1-252">By default, EF Core will map this to a single table:</span></span>

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

<span data-ttu-id="30df1-253">Tuttavia, il mapping di ogni tipo di entità a una tabella diversa comporterà invece una tabella per tipo:</span><span class="sxs-lookup"><span data-stu-id="30df1-253">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

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

<span data-ttu-id="30df1-254">Si noti che la creazione dei vincoli FOREIGN KEY mostrati in precedenza è stata aggiunta dopo aver diramato il codice per l'anteprima 8.</span><span class="sxs-lookup"><span data-stu-id="30df1-254">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="30df1-255">È possibile eseguire il mapping dei tipi di entità a tabelle diverse usando gli attributi di mapping:</span><span class="sxs-lookup"><span data-stu-id="30df1-255">Entity types can be mapped to different tables using mapping attributes:</span></span>

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

<span data-ttu-id="30df1-256">In alternativa `ModelBuilder` , usare la configurazione:</span><span class="sxs-lookup"><span data-stu-id="30df1-256">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="30df1-257">La documentazione viene rilevata in base al problema [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="30df1-257">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="30df1-258">Migrazioni: ricompila tabelle SQLite</span><span class="sxs-lookup"><span data-stu-id="30df1-258">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="30df1-259">Rispetto ad altri database, SQLite è relativamente limitato alle funzionalità di manipolazione dello schema.</span><span class="sxs-lookup"><span data-stu-id="30df1-259">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="30df1-260">Per eliminare una colonna da una tabella esistente, ad esempio, è necessario eliminare e ricreare l'intera tabella.</span><span class="sxs-lookup"><span data-stu-id="30df1-260">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="30df1-261">EF Core 5,0 le migrazioni supportano ora la ricompilazione automatica della tabella per le modifiche dello schema che lo richiedono.</span><span class="sxs-lookup"><span data-stu-id="30df1-261">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="30df1-262">Si supponga, ad esempio, che sia stata `Unicorns` creata una tabella per un `Unicorn` tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="30df1-262">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

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

<span data-ttu-id="30df1-263">Si apprenderà quindi che l'archiviazione dell'età di un unicorno viene considerata molto scortese. Rimuovere quindi la proprietà, aggiungere una nuova migrazione e aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="30df1-263">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="30df1-264">Questo aggiornamento avrà esito negativo quando si usa EF Core 3,1 perché la colonna non può essere eliminata.</span><span class="sxs-lookup"><span data-stu-id="30df1-264">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="30df1-265">In EF Core 5,0 le migrazioni ricompilano invece la tabella:</span><span class="sxs-lookup"><span data-stu-id="30df1-265">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

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

<span data-ttu-id="30df1-266">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="30df1-266">Notice that:</span></span>

* <span data-ttu-id="30df1-267">Viene creata una tabella temporanea con lo schema desiderato per la nuova tabella</span><span class="sxs-lookup"><span data-stu-id="30df1-267">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="30df1-268">I dati vengono copiati dalla tabella corrente nella tabella temporanea</span><span class="sxs-lookup"><span data-stu-id="30df1-268">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="30df1-269">L'imposizione della chiave esterna è disattivata</span><span class="sxs-lookup"><span data-stu-id="30df1-269">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="30df1-270">La tabella corrente è stata eliminata</span><span class="sxs-lookup"><span data-stu-id="30df1-270">The current table is dropped</span></span>
* <span data-ttu-id="30df1-271">La tabella temporanea è stata rinominata come nuova tabella</span><span class="sxs-lookup"><span data-stu-id="30df1-271">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="30df1-272">La documentazione viene rilevata in base al problema [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="30df1-272">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="30df1-273">Funzioni con valori di tabella</span><span class="sxs-lookup"><span data-stu-id="30df1-273">Table-valued functions</span></span>

<span data-ttu-id="30df1-274">Questa funzionalità è stata fornita dalla community da [@pmiddleton](https://github.com/pmiddleton) .</span><span class="sxs-lookup"><span data-stu-id="30df1-274">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="30df1-275">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-275">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-276">EF Core 5,0 include supporto di prima classe per il mapping di metodi .NET a funzioni con valori di tabella (funzioni con valori).</span><span class="sxs-lookup"><span data-stu-id="30df1-276">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="30df1-277">Queste funzioni possono quindi essere usate nelle query LINQ in cui anche la composizione aggiuntiva sui risultati della funzione verrà convertita in SQL.</span><span class="sxs-lookup"><span data-stu-id="30df1-277">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="30df1-278">Si consideri, ad esempio, questo TVF definito in un database SQL Server:</span><span class="sxs-lookup"><span data-stu-id="30df1-278">For example, consider this TVF defined in a SQL Server database:</span></span>

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

<span data-ttu-id="30df1-279">Il modello di EF Core richiede due tipi di entità per l'uso di TVF:</span><span class="sxs-lookup"><span data-stu-id="30df1-279">The EF Core model requires two entity types to use this TVF:</span></span>

* <span data-ttu-id="30df1-280">`Employee`Tipo che esegue il mapping alla tabella Employees in modo normale</span><span class="sxs-lookup"><span data-stu-id="30df1-280">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="30df1-281">`Report`Tipo che corrisponde alla forma restituita da TVF</span><span class="sxs-lookup"><span data-stu-id="30df1-281">A `Report` type that matches the shape returned by the TVF</span></span>

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

<span data-ttu-id="30df1-282">Questi tipi devono essere inclusi nel modello di EF Core:</span><span class="sxs-lookup"><span data-stu-id="30df1-282">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="30df1-283">Si noti che `Report` non ha una chiave primaria e che pertanto deve essere configurata come tale.</span><span class="sxs-lookup"><span data-stu-id="30df1-283">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="30df1-284">Infine, è necessario eseguire il mapping di un metodo .NET a TVF nel database.</span><span class="sxs-lookup"><span data-stu-id="30df1-284">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="30df1-285">Questo metodo può essere definito in DbContext usando il nuovo `FromExpression` Metodo:</span><span class="sxs-lookup"><span data-stu-id="30df1-285">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="30df1-286">Questo metodo usa un parametro e un tipo restituito che corrispondono ai TVF definiti in precedenza.</span><span class="sxs-lookup"><span data-stu-id="30df1-286">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="30df1-287">Il metodo viene quindi aggiunto al modello di EF Core in OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="30df1-287">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="30df1-288">(L'uso di un'espressione lambda qui è un modo semplice per passare `MethodInfo` a EF core.</span><span class="sxs-lookup"><span data-stu-id="30df1-288">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="30df1-289">Gli argomenti passati al metodo vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="30df1-289">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="30df1-290">È ora possibile scrivere query che chiamano `GetReports` e compongono i risultati.</span><span class="sxs-lookup"><span data-stu-id="30df1-290">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="30df1-291">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-291">For example:</span></span>

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

<span data-ttu-id="30df1-292">In SQL Server, viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="30df1-292">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="30df1-293">Si noti che SQL è rooted nella `Employees` tabella, chiama `GetReports` e quindi aggiunge un'altra clausola WHERE ai risultati della funzione.</span><span class="sxs-lookup"><span data-stu-id="30df1-293">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="30df1-294">Mapping di query/aggiornamento flessibile</span><span class="sxs-lookup"><span data-stu-id="30df1-294">Flexible query/update mapping</span></span>

<span data-ttu-id="30df1-295">EF Core 5,0 consente il mapping dello stesso tipo di entità a oggetti di database diversi.</span><span class="sxs-lookup"><span data-stu-id="30df1-295">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="30df1-296">Tali oggetti possono essere tabelle, viste o funzioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-296">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="30df1-297">È ad esempio possibile eseguire il mapping di un tipo di entità sia a una vista di database che a una tabella di database:</span><span class="sxs-lookup"><span data-stu-id="30df1-297">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="30df1-298">Per impostazione predefinita, EF Core effettuerà una query dalla vista e invierà gli aggiornamenti alla tabella.</span><span class="sxs-lookup"><span data-stu-id="30df1-298">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="30df1-299">Ad esempio, eseguendo il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="30df1-299">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="30df1-300">Restituisce una query sulla vista e quindi un aggiornamento alla tabella:</span><span class="sxs-lookup"><span data-stu-id="30df1-300">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="30df1-301">Configurazione della query Split a livello di contesto</span><span class="sxs-lookup"><span data-stu-id="30df1-301">Context-wide split-query configuration</span></span>

<span data-ttu-id="30df1-302">Le query suddivise (vedere di seguito) possono ora essere configurate come impostazione predefinita per qualsiasi query eseguita dal DbContext.</span><span class="sxs-lookup"><span data-stu-id="30df1-302">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="30df1-303">Questa configurazione è disponibile solo per i provider relazionali e pertanto deve essere specificata come parte della `UseProvider` configurazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-303">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="30df1-304">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-304">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="30df1-305">La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="30df1-305">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="30df1-306">Mapping di PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="30df1-306">PhysicalAddress mapping</span></span>

<span data-ttu-id="30df1-307">Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="30df1-307">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="30df1-308">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-308">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-309">La classe .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) standard viene ora mappata automaticamente a una colonna di tipo stringa per i database che non hanno già il supporto nativo.</span><span class="sxs-lookup"><span data-stu-id="30df1-309">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="30df1-310">Per ulteriori informazioni, vedere gli esempi riportati di `IPAddress` seguito.</span><span class="sxs-lookup"><span data-stu-id="30df1-310">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="30df1-311">Anteprima 7</span><span class="sxs-lookup"><span data-stu-id="30df1-311">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="30df1-312">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="30df1-312">DbContextFactory</span></span>

<span data-ttu-id="30df1-313">EF Core 5,0 introduce `AddDbContextFactory` e `AddPooledDbContextFactory` per registrare una factory per la creazione di istanze di DbContext nel contenitore di inserimento delle dipendenze dell'applicazione (D.I.).</span><span class="sxs-lookup"><span data-stu-id="30df1-313">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="30df1-314">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-314">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="30df1-315">I servizi delle applicazioni come ASP.NET Core controller possono quindi dipendere da `IDbContextFactory<TContext>` nel costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="30df1-315">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="30df1-316">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-316">For example:</span></span>

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

<span data-ttu-id="30df1-317">Le istanze di DbContext possono quindi essere create e utilizzate in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="30df1-317">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="30df1-318">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-318">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="30df1-319">Si noti che le istanze di DbContext create in questo modo _non_ sono gestite dal provider di servizi dell'applicazione e pertanto devono essere eliminate dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-319">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="30df1-320">Questa separazione è molto utile per le applicazioni blazer, in cui si consiglia l'uso di `IDbContextFactory` , ma può essere utile anche in altri scenari.</span><span class="sxs-lookup"><span data-stu-id="30df1-320">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="30df1-321">Le istanze di DbContext possono essere raggruppate chiamando `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="30df1-321">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="30df1-322">Questo pool funziona allo stesso modo di `AddDbContextPool` e presenta anche le stesse limitazioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-322">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="30df1-323">La documentazione viene rilevata in base al problema [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="30df1-323">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="30df1-324">Reimposta stato DbContext</span><span class="sxs-lookup"><span data-stu-id="30df1-324">Reset DbContext state</span></span>

<span data-ttu-id="30df1-325">EF Core 5,0 introduce `ChangeTracker.Clear()` che cancella la DbContext di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="30df1-325">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="30df1-326">Questa situazione in genere non è necessaria quando si usa la procedura consigliata per la creazione di una nuova istanza del contesto di breve durata per ogni unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="30df1-326">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="30df1-327">Tuttavia, se è necessario reimpostare lo stato di un'istanza di DbContext, l'uso del nuovo `Clear()` metodo è più efficiente e affidabile rispetto alla disconnessione di massa di tutte le entità.</span><span class="sxs-lookup"><span data-stu-id="30df1-327">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="30df1-328">La documentazione viene rilevata in base al problema [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="30df1-328">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="30df1-329">Nuovo modello per le impostazioni predefinite generate dall'archivio</span><span class="sxs-lookup"><span data-stu-id="30df1-329">New pattern for store-generated defaults</span></span>

<span data-ttu-id="30df1-330">EF Core consente l'impostazione di un valore esplicito per una colonna che può anche avere un vincolo di valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="30df1-330">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="30df1-331">EF Core usa il valore predefinito CLR del tipo di proprietà Type come sentinella per questo oggetto; Se il valore non è il valore predefinito CLR, viene inserito; in caso contrario, viene utilizzato il valore predefinito del database.</span><span class="sxs-lookup"><span data-stu-id="30df1-331">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="30df1-332">In questo modo si creano problemi per i tipi in cui il valore predefinito di CLR non è una buona sentinella, in particolare `bool` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="30df1-332">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="30df1-333">EF Core 5,0 consente ora il campo sottostante come Nullable per i casi di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="30df1-333">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="30df1-334">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-334">For example:</span></span>

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

<span data-ttu-id="30df1-335">Si noti che il campo sottostante ammette i valori null, ma la proprietà esposta pubblicamente non lo è.</span><span class="sxs-lookup"><span data-stu-id="30df1-335">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="30df1-336">In questo modo, il valore Sentinel può essere `null` senza alcun effetto sulla superficie pubblica del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="30df1-336">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="30df1-337">In questo caso, se `IsValid` non è mai impostato, verrà usato il valore predefinito del database poiché il campo sottostante rimane null.</span><span class="sxs-lookup"><span data-stu-id="30df1-337">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="30df1-338">Se `true` o `false` sono impostati, questo valore viene salvato in modo esplicito nel database.</span><span class="sxs-lookup"><span data-stu-id="30df1-338">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="30df1-339">La documentazione viene rilevata in base al problema [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="30df1-339">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="30df1-340">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="30df1-340">Cosmos partition keys</span></span>

<span data-ttu-id="30df1-341">EF Core consente di includere la chiave di partizione Cosmos nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="30df1-341">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="30df1-342">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-342">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="30df1-343">A partire dalla versione Preview 7, la chiave di partizione è inclusa nel PK del tipo di entità e viene usata per migliorare le prestazioni in alcune query.</span><span class="sxs-lookup"><span data-stu-id="30df1-343">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="30df1-344">La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="30df1-344">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="30df1-345">Configurazione di Cosmos</span><span class="sxs-lookup"><span data-stu-id="30df1-345">Cosmos configuration</span></span>

<span data-ttu-id="30df1-346">EF Core 5,0 migliora la configurazione delle connessioni Cosmos e Cosmos.</span><span class="sxs-lookup"><span data-stu-id="30df1-346">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="30df1-347">In precedenza EF Core necessario specificare in modo esplicito l'endpoint e la chiave per la connessione a un database Cosmos.</span><span class="sxs-lookup"><span data-stu-id="30df1-347">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="30df1-348">EF Core 5,0 consente invece di usare una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="30df1-348">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="30df1-349">Inoltre, EF Core 5,0 consente l'impostazione esplicita dell'istanza WebProxy.</span><span class="sxs-lookup"><span data-stu-id="30df1-349">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="30df1-350">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="30df1-351">È ora possibile configurare anche molti altri valori di timeout, limiti e così via.</span><span class="sxs-lookup"><span data-stu-id="30df1-351">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="30df1-352">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-352">For example:</span></span>

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

<span data-ttu-id="30df1-353">Infine, la modalità di connessione predefinita è ora `ConnectionMode.Gateway` , che è in genere più compatibile.</span><span class="sxs-lookup"><span data-stu-id="30df1-353">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="30df1-354">La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="30df1-354">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="30df1-355">Scaffold-DbContext ora singolari</span><span class="sxs-lookup"><span data-stu-id="30df1-355">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="30df1-356">In precedenza, durante l'impalcatura di un DbContext da un database esistente, EF Core creerà nomi di tipi di entità che corrispondono ai nomi di tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="30df1-356">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="30df1-357">Le tabelle, ad esempio, hanno `People` `Addresses` generato i tipi di entità denominati `People` e `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="30df1-357">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="30df1-358">Nelle versioni precedenti questo comportamento era configurabile tramite la registrazione di un servizio di pluralismo.</span><span class="sxs-lookup"><span data-stu-id="30df1-358">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="30df1-359">Ora, in EF Core 5,0, il pacchetto [umanizzator](https://www.nuget.org/packages/Humanizer.Core/) viene usato come servizio di pluralismo predefinito.</span><span class="sxs-lookup"><span data-stu-id="30df1-359">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="30df1-360">Ciò significa `People` che le tabelle e `Addresses` verranno ora decodificate in tipi di entità denominati `Person` e `Address` .</span><span class="sxs-lookup"><span data-stu-id="30df1-360">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="30df1-361">Salvataggio</span><span class="sxs-lookup"><span data-stu-id="30df1-361">Savepoints</span></span>

<span data-ttu-id="30df1-362">EF Core supporta ora [salvataggio](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) per un maggiore controllo sulle transazioni che eseguono più operazioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-362">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="30df1-363">Salvataggio può essere creato, rilasciato ed eseguito manualmente il rollback.</span><span class="sxs-lookup"><span data-stu-id="30df1-363">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="30df1-364">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-364">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="30df1-365">Inoltre, EF Core ora verrà eseguito il rollback all'ultimo salvataggio quando l'esecuzione `SaveChanges` non riesce.</span><span class="sxs-lookup"><span data-stu-id="30df1-365">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="30df1-366">Questo consente di ritentare SaveChanges senza ritentare l'intera transazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-366">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="30df1-367">La documentazione viene rilevata in base al problema [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="30df1-367">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="30df1-368">Preview 6</span><span class="sxs-lookup"><span data-stu-id="30df1-368">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="30df1-369">Suddividere le query per le raccolte correlate</span><span class="sxs-lookup"><span data-stu-id="30df1-369">Split queries for related collections</span></span>

<span data-ttu-id="30df1-370">A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ.</span><span class="sxs-lookup"><span data-stu-id="30df1-370">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="30df1-371">In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso.</span><span class="sxs-lookup"><span data-stu-id="30df1-371">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="30df1-372">Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.</span><span class="sxs-lookup"><span data-stu-id="30df1-372">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="30df1-373">EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="30df1-373">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="30df1-374">Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query.</span><span class="sxs-lookup"><span data-stu-id="30df1-374">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="30df1-375">È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.</span><span class="sxs-lookup"><span data-stu-id="30df1-375">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="30df1-376">Suddividere le query con Includi</span><span class="sxs-lookup"><span data-stu-id="30df1-376">Split queries with Include</span></span>

<span data-ttu-id="30df1-377">Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :</span><span class="sxs-lookup"><span data-stu-id="30df1-377">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="30df1-378">Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="30df1-378">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="30df1-379">La nuova `AsSplitQuery` API può essere usata per modificare questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="30df1-379">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="30df1-380">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-380">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="30df1-381">AsSplitQuery è disponibile per tutti i provider di database relazionali e può essere usato in qualsiasi punto della query, proprio come AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="30df1-381">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="30df1-382">EF Core genera ora le tre query SQL seguenti:</span><span class="sxs-lookup"><span data-stu-id="30df1-382">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="30df1-383">Tutte le operazioni nella radice della query sono supportate.</span><span class="sxs-lookup"><span data-stu-id="30df1-383">All operations on the query root are supported.</span></span> <span data-ttu-id="30df1-384">Sono incluse le operazioni OrderBy/Skip/Take, le operazioni di join, FirstOrDefault e le operazioni di selezione di un singolo risultato analogo.</span><span class="sxs-lookup"><span data-stu-id="30df1-384">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="30df1-385">Si noti che le inclusioni filtrate con OrderBy/Skip/Take non sono supportate nella versione Preview 6, ma sono disponibili nelle compilazioni giornaliere e saranno incluse nella versione Preview 7.</span><span class="sxs-lookup"><span data-stu-id="30df1-385">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="30df1-386">Suddividere query con proiezioni di raccolta</span><span class="sxs-lookup"><span data-stu-id="30df1-386">Split queries with collection projections</span></span>

<span data-ttu-id="30df1-387">`AsSplitQuery` può essere usato anche quando le raccolte vengono caricate nelle proiezioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-387">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="30df1-388">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-388">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="30df1-389">Questa query LINQ genera le due query SQL seguenti quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="30df1-389">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="30df1-390">Si noti che è supportata solo la materializzazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="30df1-390">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="30df1-391">Qualsiasi composizione dopo `e.Albums` il caso precedente non comporterà una query Split.</span><span class="sxs-lookup"><span data-stu-id="30df1-391">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="30df1-392">I miglioramenti apportati a questa area vengono rilevati da [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="30df1-392">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="30df1-393">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="30df1-393">IndexAttribute</span></span>

<span data-ttu-id="30df1-394">Il nuovo IndexAttribute può essere inserito in un tipo di entità per specificare un indice per una singola colonna.</span><span class="sxs-lookup"><span data-stu-id="30df1-394">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="30df1-395">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-395">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="30df1-396">Per SQL Server, le migrazioni genereranno la seguente SQL:</span><span class="sxs-lookup"><span data-stu-id="30df1-396">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="30df1-397">IndexAttribute può essere usato anche per specificare un indice che si estende su più colonne.</span><span class="sxs-lookup"><span data-stu-id="30df1-397">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="30df1-398">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-398">For example:</span></span>

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

<span data-ttu-id="30df1-399">Per SQL Server, il risultato è il seguente:</span><span class="sxs-lookup"><span data-stu-id="30df1-399">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="30df1-400">La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="30df1-400">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="30df1-401">Eccezioni di conversione query migliorate</span><span class="sxs-lookup"><span data-stu-id="30df1-401">Improved query translation exceptions</span></span>

<span data-ttu-id="30df1-402">Si sta continuando a migliorare i messaggi di eccezione generati quando la conversione di query non riesce.</span><span class="sxs-lookup"><span data-stu-id="30df1-402">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="30df1-403">Questa query, ad esempio, usa la proprietà non mappata `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="30df1-403">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="30df1-404">EF Core genererà l'eccezione seguente indicante che la conversione `IsSigned` non è riuscita perché non è stato eseguito il mapping:</span><span class="sxs-lookup"><span data-stu-id="30df1-404">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

<span data-ttu-id="30df1-405">Analogamente, vengono generati messaggi di eccezione migliori quando si tenta di tradurre i confronti di stringhe con la semantica dipendente dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="30df1-405">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="30df1-406">Questa query, ad esempio, tenta di usare `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="30df1-406">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="30df1-407">EF Core ora verrà generata l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="30df1-407">EF Core will now throw the following exception:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a><span data-ttu-id="30df1-408">Specifica ID transazione</span><span class="sxs-lookup"><span data-stu-id="30df1-408">Specify transaction ID</span></span>

<span data-ttu-id="30df1-409">Questa funzionalità è stata fornita dalla community da [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="30df1-409">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="30df1-410">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-410">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-411">EF Core espone un ID transazione per la correlazione delle transazioni tra le chiamate.</span><span class="sxs-lookup"><span data-stu-id="30df1-411">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="30df1-412">Questo ID viene in genere impostato da EF Core quando viene avviata una transazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-412">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="30df1-413">Se invece l'applicazione avvia la transazione, questa funzionalità consente all'applicazione di impostare in modo esplicito l'ID della transazione in modo che venga correlata correttamente ovunque venga utilizzata.</span><span class="sxs-lookup"><span data-stu-id="30df1-413">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="30df1-414">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-414">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="30df1-415">Mapping IPAddress</span><span class="sxs-lookup"><span data-stu-id="30df1-415">IPAddress mapping</span></span>

<span data-ttu-id="30df1-416">Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="30df1-416">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="30df1-417">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-417">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-418">La [classe IPAddress](/dotnet/api/system.net.ipaddress) .NET standard viene ora mappata automaticamente a una colonna stringa per i database che non dispongono già del supporto nativo.</span><span class="sxs-lookup"><span data-stu-id="30df1-418">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="30df1-419">Ad esempio, si consiglia di eseguire il mapping di questo tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="30df1-419">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="30df1-420">In SQL Server, le migrazioni comporteranno la creazione della tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="30df1-420">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="30df1-421">Le entità possono quindi essere aggiunte in modo normale:</span><span class="sxs-lookup"><span data-stu-id="30df1-421">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="30df1-422">E il SQL risultante inserirà l'indirizzo IPv4 o IPv6 normalizzato:</span><span class="sxs-lookup"><span data-stu-id="30df1-422">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="30df1-423">Escludi onconfigurazione durante l'impalcatura</span><span class="sxs-lookup"><span data-stu-id="30df1-423">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="30df1-424">Quando un DbContext viene sottoposto a impalcatura da un database esistente, per impostazione predefinita EF core crea un overload configurante con una stringa di connessione in modo che il contesto sia immediatamente utilizzabile.</span><span class="sxs-lookup"><span data-stu-id="30df1-424">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="30df1-425">Questa operazione non è tuttavia utile se si dispone già di una classe parziale con Configuring oppure se si sta configurando il contesto in altro modo.</span><span class="sxs-lookup"><span data-stu-id="30df1-425">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="30df1-426">Per risolvere questo problema, è ora possibile impostare i comandi di impalcatura per omettere la generazione di onconfigurazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-426">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="30df1-427">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-427">For example:</span></span>

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="30df1-428">In alternativa, nella console di gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="30df1-428">Or in the Package Manager Console:</span></span>

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="30df1-429">Si consiglia di usare [una stringa di connessione denominata e una risorsa di archiviazione sicura, ad esempio i segreti utente](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="30df1-429">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="30df1-430">Traduzioni per FirstOrDefault in stringhe</span><span class="sxs-lookup"><span data-stu-id="30df1-430">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="30df1-431">Questa funzionalità è stata fornita dalla community da [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="30df1-431">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="30df1-432">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="30df1-432">Many thanks for the contribution!</span></span>

<span data-ttu-id="30df1-433">FirstOrDefault e operatori simili per i caratteri nelle stringhe sono ora tradotti.</span><span class="sxs-lookup"><span data-stu-id="30df1-433">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="30df1-434">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="30df1-434">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="30df1-435">Verrà convertito nel codice SQL seguente quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="30df1-435">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="30df1-436">Semplificare i blocchi di case</span><span class="sxs-lookup"><span data-stu-id="30df1-436">Simplify case blocks</span></span>

<span data-ttu-id="30df1-437">EF Core genera ora query migliori con blocchi di maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="30df1-437">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="30df1-438">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="30df1-438">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="30df1-439">Was on SQL Server formalmente convertita in:</span><span class="sxs-lookup"><span data-stu-id="30df1-439">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="30df1-440">Ma ora viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="30df1-440">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="30df1-441">Preview 5</span><span class="sxs-lookup"><span data-stu-id="30df1-441">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="30df1-442">Regole di confronto di database</span><span class="sxs-lookup"><span data-stu-id="30df1-442">Database collations</span></span>

<span data-ttu-id="30df1-443">È ora possibile specificare le regole di confronto predefinite per un database nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="30df1-443">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="30df1-444">Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database.</span><span class="sxs-lookup"><span data-stu-id="30df1-444">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="30df1-445">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-445">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="30df1-446">Migrations genera quindi quanto segue per creare il database in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="30df1-446">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="30df1-447">È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche.</span><span class="sxs-lookup"><span data-stu-id="30df1-447">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="30df1-448">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-448">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="30df1-449">Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.</span><span class="sxs-lookup"><span data-stu-id="30df1-449">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="30df1-450">Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse.</span><span class="sxs-lookup"><span data-stu-id="30df1-450">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="30df1-451">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-451">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="30df1-452">Verrà generata la query seguente per SQL Server:</span><span class="sxs-lookup"><span data-stu-id="30df1-452">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="30df1-453">Si noti che le regole di confronto ad hoc devono essere utilizzate con cautela, in quanto possono influire negativamente sulle prestazioni del database.</span><span class="sxs-lookup"><span data-stu-id="30df1-453">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="30df1-454">La documentazione viene rilevata in base al problema [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="30df1-454">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="30df1-455">Argomenti del flusso in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="30df1-455">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="30df1-456">Gli argomenti vengono ora propagati dalla riga di comando nel `CreateDbContext` metodo di [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span><span class="sxs-lookup"><span data-stu-id="30df1-456">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="30df1-457">Per indicare, ad esempio, che si tratta di una build di sviluppo, è possibile passare un argomento personalizzato, ad esempio, `dev` nella riga di comando:</span><span class="sxs-lookup"><span data-stu-id="30df1-457">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```console
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="30df1-458">Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto.</span><span class="sxs-lookup"><span data-stu-id="30df1-458">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="30df1-459">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-459">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="30df1-460">La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="30df1-460">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="30df1-461">Query senza rilevamento con risoluzione di identità</span><span class="sxs-lookup"><span data-stu-id="30df1-461">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="30df1-462">È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità.</span><span class="sxs-lookup"><span data-stu-id="30df1-462">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="30df1-463">La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="30df1-463">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="30df1-464">Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:</span><span class="sxs-lookup"><span data-stu-id="30df1-464">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="30df1-465">Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="30df1-465">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="30df1-466">Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata.</span><span class="sxs-lookup"><span data-stu-id="30df1-466">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="30df1-467">Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="30df1-467">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="30df1-468">La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="30df1-468">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="30df1-469">Colonne calcolate (rese permanente) archiviate</span><span class="sxs-lookup"><span data-stu-id="30df1-469">Stored (persisted) computed columns</span></span>

<span data-ttu-id="30df1-470">La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo.</span><span class="sxs-lookup"><span data-stu-id="30df1-470">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="30df1-471">Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore.</span><span class="sxs-lookup"><span data-stu-id="30df1-471">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="30df1-472">Consente inoltre di indicizzare la colonna per alcuni database.</span><span class="sxs-lookup"><span data-stu-id="30df1-472">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="30df1-473">EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate.</span><span class="sxs-lookup"><span data-stu-id="30df1-473">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="30df1-474">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-474">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="30df1-475">Colonne calcolate SQLite</span><span class="sxs-lookup"><span data-stu-id="30df1-475">SQLite computed columns</span></span>

<span data-ttu-id="30df1-476">EF Core supporta ora le colonne calcolate nei database SQLite.</span><span class="sxs-lookup"><span data-stu-id="30df1-476">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="30df1-477">Preview 4</span><span class="sxs-lookup"><span data-stu-id="30df1-477">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="30df1-478">Configurare la precisione del database/ridimensionare il modello</span><span class="sxs-lookup"><span data-stu-id="30df1-478">Configure database precision/scale in model</span></span>

<span data-ttu-id="30df1-479">È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli.</span><span class="sxs-lookup"><span data-stu-id="30df1-479">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="30df1-480">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-480">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="30df1-481">La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="30df1-481">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="30df1-482">La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="30df1-482">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="30df1-483">Specificare il fattore di riempimento dell'indice SQL Server</span><span class="sxs-lookup"><span data-stu-id="30df1-483">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="30df1-484">È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="30df1-484">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="30df1-485">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-485">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="30df1-486">Preview 3</span><span class="sxs-lookup"><span data-stu-id="30df1-486">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="30df1-487">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="30df1-487">Filtered Include</span></span>

<span data-ttu-id="30df1-488">Il metodo include ora supporta il filtraggio delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="30df1-488">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="30df1-489">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-489">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="30df1-490">Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".</span><span class="sxs-lookup"><span data-stu-id="30df1-490">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="30df1-491">È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse.</span><span class="sxs-lookup"><span data-stu-id="30df1-491">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="30df1-492">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-492">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

<span data-ttu-id="30df1-493">Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.</span><span class="sxs-lookup"><span data-stu-id="30df1-493">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="30df1-494">Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="30df1-494">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="30df1-495">Nuova API ModelBuilder per le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="30df1-495">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="30df1-496">Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="30df1-496">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="30df1-497">Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="30df1-497">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="30df1-498">Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:</span><span class="sxs-lookup"><span data-stu-id="30df1-498">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="30df1-499">Si noti che l' `Navigation` API non sostituisce la configurazione della relazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-499">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="30df1-500">Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.</span><span class="sxs-lookup"><span data-stu-id="30df1-500">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="30df1-501">Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="30df1-501">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="30df1-502">Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="30df1-502">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="30df1-503">Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando.</span><span class="sxs-lookup"><span data-stu-id="30df1-503">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="30df1-504">Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi:</span><span class="sxs-lookup"><span data-stu-id="30df1-504">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="30df1-505">Per informazioni dettagliate, vedere la documentazione sulle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e la [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="30df1-505">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="30df1-506">Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:</span><span class="sxs-lookup"><span data-stu-id="30df1-506">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="30df1-507">Per informazioni dettagliate, vedere la [documentazione degli strumenti](xref:core/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="30df1-507">See the [Tools documentation](xref:core/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="30df1-508">Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="30df1-508">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="30df1-509">EnableDetailedErrors ha restituito</span><span class="sxs-lookup"><span data-stu-id="30df1-509">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="30df1-510">Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database.</span><span class="sxs-lookup"><span data-stu-id="30df1-510">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="30df1-511">Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.</span><span class="sxs-lookup"><span data-stu-id="30df1-511">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="30df1-512">`EnableDetailedErrors`Se si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.</span><span class="sxs-lookup"><span data-stu-id="30df1-512">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="30df1-513">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-513">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="30df1-514">La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="30df1-514">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="30df1-515">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="30df1-515">Cosmos partition keys</span></span>

<span data-ttu-id="30df1-516">La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query.</span><span class="sxs-lookup"><span data-stu-id="30df1-516">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="30df1-517">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-517">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="30df1-518">La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="30df1-518">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="30df1-519">Supporto per la funzione SQL Server DataLength</span><span class="sxs-lookup"><span data-stu-id="30df1-519">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="30df1-520">È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo.</span><span class="sxs-lookup"><span data-stu-id="30df1-520">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="30df1-521">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-521">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="30df1-522">Preview 2</span><span class="sxs-lookup"><span data-stu-id="30df1-522">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="30df1-523">Usare un attributo C# per specificare un campo di supporto delle proprietà</span><span class="sxs-lookup"><span data-stu-id="30df1-523">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="30df1-524">È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà.</span><span class="sxs-lookup"><span data-stu-id="30df1-524">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="30df1-525">Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="30df1-525">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="30df1-526">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-526">For example:</span></span>

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

<span data-ttu-id="30df1-527">La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="30df1-527">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="30df1-528">Mapping completo del discriminatore</span><span class="sxs-lookup"><span data-stu-id="30df1-528">Complete discriminator mapping</span></span>

<span data-ttu-id="30df1-529">EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="30df1-529">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="30df1-530">Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore.</span><span class="sxs-lookup"><span data-stu-id="30df1-530">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="30df1-531">EF Core 5,0 implementa ora questi miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="30df1-531">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="30df1-532">Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:</span><span class="sxs-lookup"><span data-stu-id="30df1-532">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="30df1-533">EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:</span><span class="sxs-lookup"><span data-stu-id="30df1-533">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="30df1-534">Si tratta del comportamento predefinito che inizia con Preview 3.</span><span class="sxs-lookup"><span data-stu-id="30df1-534">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="30df1-535">Miglioramenti delle prestazioni in Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="30df1-535">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="30df1-536">Sono stati apportati due miglioramenti alle prestazioni per SQLIte:</span><span class="sxs-lookup"><span data-stu-id="30df1-536">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="30df1-537">Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.</span><span class="sxs-lookup"><span data-stu-id="30df1-537">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="30df1-538">L'inizializzazione di SqliteConnection è ora Lazy.</span><span class="sxs-lookup"><span data-stu-id="30df1-538">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="30df1-539">Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.</span><span class="sxs-lookup"><span data-stu-id="30df1-539">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="30df1-540">Preview 1</span><span class="sxs-lookup"><span data-stu-id="30df1-540">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="30df1-541">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="30df1-541">Simple logging</span></span>

<span data-ttu-id="30df1-542">Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6.</span><span class="sxs-lookup"><span data-stu-id="30df1-542">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="30df1-543">Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.</span><span class="sxs-lookup"><span data-stu-id="30df1-543">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="30df1-544">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="30df1-544">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="30df1-545">La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="30df1-545">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="30df1-546">Modo semplice per ottenere SQL generato</span><span class="sxs-lookup"><span data-stu-id="30df1-546">Simple way to get generated SQL</span></span>

<span data-ttu-id="30df1-547">EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="30df1-547">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="30df1-548">La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="30df1-548">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="30df1-549">La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="30df1-549">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="30df1-550">Usare un attributo C# per indicare che un'entità non ha una chiave</span><span class="sxs-lookup"><span data-stu-id="30df1-550">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="30df1-551">È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="30df1-551">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="30df1-552">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-552">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="30df1-553">La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="30df1-553">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="30df1-554">La stringa di connessione o di connessione può essere modificata in DbContext inizializzato</span><span class="sxs-lookup"><span data-stu-id="30df1-554">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="30df1-555">È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="30df1-555">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="30df1-556">Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="30df1-556">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="30df1-557">Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.</span><span class="sxs-lookup"><span data-stu-id="30df1-557">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="30df1-558">La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="30df1-558">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="30df1-559">Proxy di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="30df1-559">Change-tracking proxies</span></span>

<span data-ttu-id="30df1-560">EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) e [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="30df1-560">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="30df1-561">Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="30df1-561">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="30df1-562">Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.</span><span class="sxs-lookup"><span data-stu-id="30df1-562">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="30df1-563">La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="30df1-563">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="30df1-564">Viste di debug migliorate</span><span class="sxs-lookup"><span data-stu-id="30df1-564">Enhanced debug views</span></span>

<span data-ttu-id="30df1-565">Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi.</span><span class="sxs-lookup"><span data-stu-id="30df1-565">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="30df1-566">Una visualizzazione di debug per il modello è stata implementata qualche tempo fa.</span><span class="sxs-lookup"><span data-stu-id="30df1-566">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="30df1-567">Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.</span><span class="sxs-lookup"><span data-stu-id="30df1-567">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="30df1-568">La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="30df1-568">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="30df1-569">La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="30df1-569">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="30df1-570">Gestione migliorata della semantica null del database</span><span class="sxs-lookup"><span data-stu-id="30df1-570">Improved handling of database null semantics</span></span>

<span data-ttu-id="30df1-571">I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL.</span><span class="sxs-lookup"><span data-stu-id="30df1-571">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="30df1-572">Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null.</span><span class="sxs-lookup"><span data-stu-id="30df1-572">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="30df1-573">EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#.</span><span class="sxs-lookup"><span data-stu-id="30df1-573">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="30df1-574">EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.</span><span class="sxs-lookup"><span data-stu-id="30df1-574">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="30df1-575">La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="30df1-575">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="30df1-576">Proprietà indicizzatore</span><span class="sxs-lookup"><span data-stu-id="30df1-576">Indexer properties</span></span>

<span data-ttu-id="30df1-577">EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#.</span><span class="sxs-lookup"><span data-stu-id="30df1-577">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="30df1-578">Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="30df1-578">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="30df1-579">La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="30df1-579">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="30df1-580">Generazione di vincoli check per i mapping enum</span><span class="sxs-lookup"><span data-stu-id="30df1-580">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="30df1-581">EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="30df1-581">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="30df1-582">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-582">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="30df1-583">La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="30df1-583">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="30df1-584">Relazionale</span><span class="sxs-lookup"><span data-stu-id="30df1-584">IsRelational</span></span>

<span data-ttu-id="30df1-585">`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="30df1-585">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="30df1-586">Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale.</span><span class="sxs-lookup"><span data-stu-id="30df1-586">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="30df1-587">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-587">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="30df1-588">La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="30df1-588">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="30df1-589">Concorrenza ottimistica di Cosmos con ETag</span><span class="sxs-lookup"><span data-stu-id="30df1-589">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="30df1-590">Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag.</span><span class="sxs-lookup"><span data-stu-id="30df1-590">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="30df1-591">Usare il generatore di modelli in OnModelCreating per configurare un ETag:</span><span class="sxs-lookup"><span data-stu-id="30df1-591">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="30df1-592">SaveChanges genera quindi un'operazione `DbUpdateConcurrencyException` in un conflitto di concorrenza, che [può essere gestita](xref:core/saving/concurrency) per implementare nuovi tentativi e così via.</span><span class="sxs-lookup"><span data-stu-id="30df1-592">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="30df1-593">La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="30df1-593">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="30df1-594">Eseguire query sulle traduzioni per più costrutti DateTime</span><span class="sxs-lookup"><span data-stu-id="30df1-594">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="30df1-595">Vengono ora convertite le query contenenti nuove costruzioni DateTime.</span><span class="sxs-lookup"><span data-stu-id="30df1-595">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="30df1-596">Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:</span><span class="sxs-lookup"><span data-stu-id="30df1-596">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="30df1-597">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="30df1-597">DateDiffWeek</span></span>
* <span data-ttu-id="30df1-598">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="30df1-598">DateFromParts</span></span>

<span data-ttu-id="30df1-599">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-599">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="30df1-600">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="30df1-600">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="30df1-601">Eseguire query sulle traduzioni per altri costrutti di matrici di byte</span><span class="sxs-lookup"><span data-stu-id="30df1-601">Query translations for more byte array constructs</span></span>

<span data-ttu-id="30df1-602">Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.</span><span class="sxs-lookup"><span data-stu-id="30df1-602">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="30df1-603">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="30df1-603">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="30df1-604">La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="30df1-604">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="30df1-605">Conversione di query per invertire</span><span class="sxs-lookup"><span data-stu-id="30df1-605">Query translation for Reverse</span></span>

<span data-ttu-id="30df1-606">Le query che utilizzano `Reverse` ora vengono convertite.</span><span class="sxs-lookup"><span data-stu-id="30df1-606">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="30df1-607">Esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-607">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="30df1-608">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="30df1-608">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="30df1-609">Conversione di query per operatori bit per bit</span><span class="sxs-lookup"><span data-stu-id="30df1-609">Query translation for bitwise operators</span></span>

<span data-ttu-id="30df1-610">Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="30df1-610">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="30df1-611">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="30df1-611">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="30df1-612">Conversione di query per le stringhe in Cosmos</span><span class="sxs-lookup"><span data-stu-id="30df1-612">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="30df1-613">Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30df1-613">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="30df1-614">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="30df1-614">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
