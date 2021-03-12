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
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="5e2f5-103">Novità di EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="5e2f5-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="5e2f5-104">Nell'elenco seguente sono incluse le nuove funzionalità principali di EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="5e2f5-105">Per l'elenco completo dei problemi nella versione, vedere Microsoft [Issue Tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="5e2f5-106">Come versione principale, EF Core 5,0 contiene anche alcune [modifiche di rilievo](xref:core/what-is-new/ef-core-5.0/breaking-changes), ovvero miglioramenti dell'API o modifiche comportamentali che possono avere un impatto negativo sulle applicazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="5e2f5-107">Molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="5e2f5-107">Many-to-many</span></span>

<span data-ttu-id="5e2f5-108">EF Core 5,0 supporta relazioni molti-a-molti senza eseguire in modo esplicito il mapping della tabella di join.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="5e2f5-109">Si considerino, ad esempio, i tipi di entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-109">For example, consider these entity types:</span></span>

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

<span data-ttu-id="5e2f5-110">EF Core 5,0 riconosce questa relazione come una relazione molti-a-molti per convenzione e crea automaticamente una `PostTag` tabella di join nel database.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="5e2f5-111">I dati possono essere sottoposti a query e aggiornati senza fare riferimento in modo esplicito alla tabella di join, semplificando notevolmente il codice.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="5e2f5-112">La tabella di join può comunque essere personalizzata e sottoposta a query in modo esplicito, se necessario.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="5e2f5-113">Per ulteriori informazioni, [vedere la documentazione completa su molti-a-molti](xref:core/modeling/relationships#many-to-many).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="5e2f5-114">Suddividere le query</span><span class="sxs-lookup"><span data-stu-id="5e2f5-114">Split queries</span></span>

<span data-ttu-id="5e2f5-115">A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="5e2f5-116">In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="5e2f5-117">Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="5e2f5-118">EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="5e2f5-119">Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="5e2f5-120">È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="5e2f5-121">Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :</span><span class="sxs-lookup"><span data-stu-id="5e2f5-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="5e2f5-122">Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="5e2f5-123">Con le query Split viene invece generato il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="5e2f5-124">È possibile abilitare le query Split inserendo il nuovo `AsSplitQuery` operatore in un punto qualsiasi della query LINQ o globalmente nell'oggetto del modello `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="5e2f5-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="5e2f5-125">Per ulteriori informazioni, [vedere la documentazione completa sulle query suddivise](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="5e2f5-126">Registrazione semplice e diagnostica migliorata</span><span class="sxs-lookup"><span data-stu-id="5e2f5-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="5e2f5-127">EF Core 5,0 introduce un modo semplice per impostare la registrazione tramite il nuovo `LogTo` metodo.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="5e2f5-128">Il codice seguente causerà la scrittura dei messaggi di registrazione nella console, inclusi tutti i SQL generati da EF Core:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="5e2f5-129">Inoltre, è ora possibile chiamare `ToQueryString` su qualsiasi query LINQ, recuperando il SQL che verrà eseguito dalla query:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="5e2f5-130">Infine, vari tipi di EF Core sono stati inseriti con una `DebugView` Proprietà migliorata che fornisce una visualizzazione dettagliata degli elementi interni.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="5e2f5-131">Ad esempio, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> è possibile consultare per verificare esattamente quali entità vengono registrate in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="5e2f5-132">Per ulteriori informazioni, [vedere la documentazione relativa alla registrazione e all'intercettazione](xref:core/logging-events-diagnostics/index).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="5e2f5-133">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="5e2f5-133">Filtered include</span></span>

<span data-ttu-id="5e2f5-134">Il `Include` Metodo ora supporta il filtraggio delle entità incluse:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="5e2f5-135">Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".</span><span class="sxs-lookup"><span data-stu-id="5e2f5-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="5e2f5-136">Per ulteriori informazioni, [vedere la documentazione completa sull'inclusione filtrata](xref:core/querying/related-data/eager#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-136">For further information, [see the full documentation on filtered include](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="5e2f5-137">Mapping tabella per tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="5e2f5-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="5e2f5-138">Per impostazione predefinita, EF Core esegue il mapping di una gerarchia di ereditarietà di tipi .NET a una singola tabella di database.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="5e2f5-139">Questa operazione è nota come mapping tabella per gerarchia (TPH).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="5e2f5-140">EF Core 5,0 consente inoltre di eseguire il mapping di ogni tipo .NET in una gerarchia di ereditarietà a una tabella di database differente. noto come mapping tabella per tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="5e2f5-141">Si consideri, ad esempio, questo modello con una gerarchia mappata:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-141">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="5e2f5-142">Con TPT, viene creata una tabella di database per ogni tipo nella gerarchia:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

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

<span data-ttu-id="5e2f5-143">Per ulteriori informazioni, [vedere la documentazione completa su TPT](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="5e2f5-144">Mapping di entità flessibili</span><span class="sxs-lookup"><span data-stu-id="5e2f5-144">Flexible entity mapping</span></span>

<span data-ttu-id="5e2f5-145">I tipi di entità vengono in genere mappati a tabelle o viste in modo che EF Core effettuerà il pull del contenuto della tabella o della vista durante l'esecuzione di query per quel tipo.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="5e2f5-146">EF Core 5,0 aggiunge opzioni di mapping aggiuntive, in cui è possibile eseguire il mapping di un'entità a una query SQL (denominata "query di definizione") o a una funzione con valori di tabella (TVF):</span><span class="sxs-lookup"><span data-stu-id="5e2f5-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

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

<span data-ttu-id="5e2f5-147">È anche possibile eseguire il mapping delle funzioni con valori di tabella a un metodo .NET anziché a un DbSet, consentendo il passaggio di parametri. il mapping può essere impostato con <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> .</span><span class="sxs-lookup"><span data-stu-id="5e2f5-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="5e2f5-148">Infine, è ora possibile eseguire il mapping di un'entità a una vista durante l'esecuzione di query (o a una funzione o la definizione di una query), ma a una tabella durante l'aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="5e2f5-149">Tipi di entità e contenitori di proprietà di tipo condiviso</span><span class="sxs-lookup"><span data-stu-id="5e2f5-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="5e2f5-150">EF Core 5,0 consente di eseguire il mapping dello stesso tipo CLR a più tipi di entità diversi. tali tipi sono noti come tipi di entità di tipo condiviso.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="5e2f5-151">Sebbene sia possibile usare qualsiasi tipo CLR con questa funzionalità, .NET `Dictionary` offre un caso d'uso particolarmente interessante, che viene chiamato "sacchetti delle proprietà":</span><span class="sxs-lookup"><span data-stu-id="5e2f5-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

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

<span data-ttu-id="5e2f5-152">È quindi possibile eseguire query su queste entità e aggiornarle esattamente come i normali tipi di entità con il proprio tipo CLR dedicato.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="5e2f5-153">Ulteriori informazioni sono disponibili nella documentazione relativa a [contenitori di proprietà](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="5e2f5-154">Dipendenti 1:1 richiesti</span><span class="sxs-lookup"><span data-stu-id="5e2f5-154">Required 1:1 dependents</span></span>

<span data-ttu-id="5e2f5-155">In EF Core 3,1, l'entità finale dipendente di una relazione uno-a-uno è sempre considerata facoltativa.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="5e2f5-156">Questo è stato più evidente quando si usano le entità di proprietà, perché tutte le colonne dell'entità di proprietà sono state create come nullable nel database, anche se sono state configurate come richieste nel modello.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="5e2f5-157">In EF Core 5,0, una navigazione a un'entità di proprietà può essere configurata come dipendente obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="5e2f5-158">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-158">For example:</span></span>

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

## <a name="dbcontextfactory"></a><span data-ttu-id="5e2f5-159">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="5e2f5-159">DbContextFactory</span></span>

<span data-ttu-id="5e2f5-160">EF Core 5,0 introduce `AddDbContextFactory` e `AddPooledDbContextFactory` per registrare una factory per la creazione di istanze DbContext nel contenitore di inserimento delle dipendenze dell'applicazione (D.I.). questa operazione può essere utile quando il codice dell'applicazione deve creare ed eliminare manualmente le istanze del contesto.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="5e2f5-161">A questo punto, è possibile inserire i servizi delle applicazioni come ASP.NET Core controller `IDbContextFactory<TContext>` e usarli per creare un'istanza delle istanze di contesto:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

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

<span data-ttu-id="5e2f5-162">Per ulteriori informazioni, [vedere la documentazione completa su DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="5e2f5-163">Ricompilazioni tabella SQLite</span><span class="sxs-lookup"><span data-stu-id="5e2f5-163">SQLite table rebuilds</span></span>

<span data-ttu-id="5e2f5-164">Rispetto ad altri database, SQLite è relativamente limitato nelle funzionalità di manipolazione dello schema; l'eliminazione di una colonna da una tabella esistente, ad esempio, non è supportata.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="5e2f5-165">EF Core 5,0 ovviare a queste limitazioni creando automaticamente una nuova tabella, copiando i dati dalla vecchia tabella, eliminando la tabella precedente e rinominando quella nuova.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="5e2f5-166">Questa operazione "ricompila" la tabella e consente di applicare in modo sicuro le operazioni di migrazione non supportate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="5e2f5-167">Per informazioni dettagliate sulle operazioni di migrazione supportate tramite ricompilazioni di tabella, [vedere questa pagina della documentazione](xref:core/providers/sqlite/limitations#migrations-limitations).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="5e2f5-168">Regole di confronto di database</span><span class="sxs-lookup"><span data-stu-id="5e2f5-168">Database collations</span></span>

<span data-ttu-id="5e2f5-169">EF Core 5,0 introduce il supporto per specificare le regole di confronto del testo a livello di database, di colonna o di query.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="5e2f5-170">Questo consente di configurare la distinzione tra maiuscole e minuscole e altri aspetti testuali in modo flessibile e non compromettere le prestazioni di esecuzione delle query.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="5e2f5-171">Il codice seguente, ad esempio, configurerà la `Name` colonna in modo da fare distinzione tra maiuscole e minuscole in SQL Server e tutti gli indici creati sulla colonna funzioneranno in modo appropriato:</span><span class="sxs-lookup"><span data-stu-id="5e2f5-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="5e2f5-172">Per ulteriori informazioni, [vedere la documentazione completa sulle regole di confronto e la distinzione tra maiuscole e minuscole](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="5e2f5-173">Contatori di eventi</span><span class="sxs-lookup"><span data-stu-id="5e2f5-173">Event counters</span></span>

<span data-ttu-id="5e2f5-174">EF Core 5,0 espone i [contatori degli eventi](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) che possono essere usati per tenere traccia delle prestazioni dell'applicazione e individuare varie anomalie.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="5e2f5-175">È sufficiente connettersi a un processo che esegue EF con lo strumento [DotNet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) :</span><span class="sxs-lookup"><span data-stu-id="5e2f5-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

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

<span data-ttu-id="5e2f5-176">Per ulteriori informazioni, [vedere la documentazione completa sui contatori degli eventi](xref:core/logging-events-diagnostics/event-counters).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="5e2f5-177">Altre funzionalità</span><span class="sxs-lookup"><span data-stu-id="5e2f5-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="5e2f5-178">Creazione di modelli</span><span class="sxs-lookup"><span data-stu-id="5e2f5-178">Model building</span></span>

* <span data-ttu-id="5e2f5-179">Sono state introdotte API per la creazione di modelli per semplificare la configurazione degli [operatori di confronto dei valori](xref:core/modeling/value-comparers).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="5e2f5-180">Le colonne calcolate ora possono essere configurate come [ *archiviate* o *virtuali*](xref:core/modeling/generated-properties#computed-columns).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="5e2f5-181">La precisione e la scala possono ora essere configurate [tramite l'API Fluent](xref:core/modeling/entity-properties#precision-and-scale).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="5e2f5-182">Sono state introdotte nuove API per la creazione di modelli per le [proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="5e2f5-183">Sono state introdotte nuove API per la creazione di modelli per i campi, in modo analogo alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="5e2f5-184">È ora possibile eseguire il mapping dei tipi .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) e [IPAddress](/dotnet/api/system.net.ipaddress) alle colonne stringa del database.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="5e2f5-185">È ora possibile configurare un campo sottostante tramite [il nuovo `[BackingField]` attributo](xref:core/modeling/backing-field).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="5e2f5-186">Sono ora consentiti i campi di supporto Nullable, per offrire un supporto migliore per le impostazioni predefinite generate dall'archivio in cui il valore predefinito di CLR non è un valore sentinella valido (rilevante `bool` ).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="5e2f5-187">Un nuovo `[Index]` attributo può essere usato in un tipo di entità per specificare un indice, anziché usare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="5e2f5-188">Un nuovo `[Keyless]` attributo può essere utilizzato per configurare un tipo di entità [senza chiave](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="5e2f5-189">Per impostazione predefinita, [EF Core considera ora i discriminatori come *completi*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), ovvero prevede di non visualizzare mai i valori del discriminatore non configurati dall'applicazione nel modello.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="5e2f5-190">Questo consente di migliorare le prestazioni e può essere disabilitato se la colonna del discriminatore potrebbe contenere valori sconosciuti.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="5e2f5-191">Query</span><span class="sxs-lookup"><span data-stu-id="5e2f5-191">Query</span></span>

* <span data-ttu-id="5e2f5-192">Le eccezioni di errore di conversione delle query ora contengono motivi più espliciti relativi ai motivi dell'errore, per facilitare l'individuazione del problema.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="5e2f5-193">Le query senza rilevamento possono ora eseguire la [risoluzione delle identità](xref:core/querying/tracking#identity-resolution), evitando la restituzione di più istanze di entità per lo stesso oggetto di database.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="5e2f5-194">Aggiunta del supporto per GroupBy con aggregazioni condizionali, ad `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))` esempio.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="5e2f5-195">Aggiunta del supporto per la conversione dell'operatore DISTINCT sugli elementi del gruppo prima dell'aggregazione.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="5e2f5-196">Traduzione di [`Reverse`](/dotnet/api/system.linq.queryable.reverse) .</span><span class="sxs-lookup"><span data-stu-id="5e2f5-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="5e2f5-197">Traduzione migliorata `DateTime` per SQL Server (ad esempio [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) , [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) ).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="5e2f5-198">Conversione di nuovi metodi in matrici di byte (ad esempio [`Contains`](/dotnet/api/system.linq.enumerable.contains) , [`Length`](/dotnet/api/system.array.length) , [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal) ).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="5e2f5-199">Conversione di alcuni operatori bit per bit aggiuntivi, ad esempio il complemento a due.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="5e2f5-200">Conversione di `FirstOrDefault` su stringhe.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="5e2f5-201">Migliore conversione delle query in termini di semantica null, ottenendo query più rigorose ed efficienti.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="5e2f5-202">È ora possibile aggiungere annotazioni alle funzioni mappate dall'utente per controllare la propagazione Null, ottenendo nuovamente query più efficienti e più efficienti.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="5e2f5-203">I blocchi di CASE SQL contenenti sono ora molto più concisi.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="5e2f5-204">[`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql)È ora possibile chiamare la funzione SQL Server nelle query tramite il nuovo [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) metodo.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="5e2f5-205">`EnableDetailedErrors` aggiunge [ulteriori dettagli alle eccezioni](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="5e2f5-206">Saving</span><span class="sxs-lookup"><span data-stu-id="5e2f5-206">Saving</span></span>

* <span data-ttu-id="5e2f5-207">[Intercettazione](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) ed [eventi](xref:core/logging-events-diagnostics/events)di SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="5e2f5-208">Sono state introdotte API per il controllo di [salvataggio di transazione](xref:core/saving/transactions#savepoints).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="5e2f5-209">Inoltre, EF Core creerà automaticamente un salvataggio quando `SaveChanges` viene chiamato e una transazione è già in corso e ne viene eseguito il rollback in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="5e2f5-210">Un ID transazione può essere impostato in modo esplicito dall'applicazione, consentendo una correlazione più semplice degli eventi di transazione nella registrazione e altrove.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="5e2f5-211">Le dimensioni massime del batch predefinite per SQL Server sono state modificate in 42 in base a un'analisi delle prestazioni in batch.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="5e2f5-212">Migrazioni e ponteggi</span><span class="sxs-lookup"><span data-stu-id="5e2f5-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="5e2f5-213">È ora possibile [escludere le tabelle dalle migrazioni](xref:core/modeling/entity-types#excluding-from-migrations).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="5e2f5-214">Un nuovo [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) comando ora Mostra le migrazioni che non sono state ancora applicate al database ( [`Get-Migration`](xref:core/cli/powershell#get-migration) esegue la stessa operazione nella Console di gestione pacchetti).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="5e2f5-215">Gli script delle migrazioni ora contengono istruzioni di transazione laddove appropriate per migliorare la gestione dei casi in cui l'applicazione di migrazione non riesce.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="5e2f5-216">Le colonne per le classi di base senza mapping sono ora ordinate dopo altre colonne per i tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="5e2f5-217">Si noti che questo influisca solo sulle tabelle appena create. l'ordine delle colonne per le tabelle esistenti rimane invariato.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="5e2f5-218">La generazione della migrazione ora può essere consapevole se la migrazione generata è idempotente e se l'output verrà eseguito immediatamente o generato come uno script.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="5e2f5-219">Sono stati aggiunti nuovi parametri della riga di comando per specificare gli spazi dei nomi nelle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e nell' [impalcatura](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="5e2f5-220">Il comando [DotNet EF database Update](xref:core/cli/dotnet#dotnet-ef-database-update) ora accetta un nuovo `--connection` parametro per specificare la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="5e2f5-221">L'impalcatura dei database esistenti ora singolari i nomi di tabella, quindi le tabelle denominate e verranno sottoponite `People` `Addresses` a ponteggi ai tipi di entità chiamati `Person` e `Address` .</span><span class="sxs-lookup"><span data-stu-id="5e2f5-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="5e2f5-222">[I nomi dei database originali possono comunque essere conservati](xref:core/managing-schemas/scaffolding#preserving-names).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="5e2f5-223">La nuova [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) opzione può indicare EF core da escludere `OnModelConfiguring` durante l'impalcatura di un modello.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="5e2f5-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="5e2f5-224">Cosmos</span></span>

* <span data-ttu-id="5e2f5-225">[Le impostazioni di connessione Cosmos](xref:core/providers/cosmos/index#cosmos-options) sono state espanse.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="5e2f5-226">La concorrenza ottimistica è ora [supportata in Cosmos tramite l'uso di ETag](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="5e2f5-227">Il nuovo `WithPartitionKey` metodo consente di includere la [chiave di partizione](xref:core/providers/cosmos/index#partition-keys) Cosmos sia nel modello che nelle query.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="5e2f5-228">I metodi di [`Contains`](/dotnet/api/system.string.contains) stringa [`StartsWith`](/dotnet/api/system.string.startswith) e [`EndsWith`](/dotnet/api/system.string.endswith) sono ora tradotti per Cosmos.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="5e2f5-229">L' `is` operatore C# è ora convertito in Cosmos.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="5e2f5-230">SQLite</span><span class="sxs-lookup"><span data-stu-id="5e2f5-230">Sqlite</span></span>

* <span data-ttu-id="5e2f5-231">Sono ora supportate le colonne calcolate.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="5e2f5-232">Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="5e2f5-233">L'inizializzazione di SqliteConnection è ora Lazy.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="5e2f5-234">Altri</span><span class="sxs-lookup"><span data-stu-id="5e2f5-234">Other</span></span>

* <span data-ttu-id="5e2f5-235">È possibile generare proxy di rilevamento delle modifiche che implementano automaticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) e [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="5e2f5-236">Questo approccio offre un approccio alternativo al rilevamento delle modifiche che non esegue l'analisi delle modifiche quando `SaveChanges` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="5e2f5-237"><xref:System.Data.Common.DbConnection>È ora possibile modificare una stringa di connessione o in un DbContext già inizializzato.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="5e2f5-238">Il nuovo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> metodo. 0 # Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) cancella il DbContext di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="5e2f5-239">Questa situazione in genere non è necessaria quando si usa la procedura consigliata per la creazione di una nuova istanza del contesto di breve durata per ogni unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="5e2f5-240">Tuttavia, se è necessario reimpostare lo stato di un'istanza di DbContext, l'uso del nuovo `Clear()` metodo è più efficiente e affidabile rispetto alla disconnessione di massa per tutte le entità.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="5e2f5-241">Gli strumenti da riga di comando EF Core ora configurano automaticamente le `ASPNETCORE_ENVIRONMENT`  `DOTNET_ENVIRONMENT` variabili di ambiente e in "Development".</span><span class="sxs-lookup"><span data-stu-id="5e2f5-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="5e2f5-242">In questo modo, quando si usa l'host generico in linea con l'esperienza ASP.NET Core durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="5e2f5-243">Gli argomenti della riga di comando personalizzati possono essere propagati in <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> , consentendo alle applicazioni di controllare la modalità di creazione e inizializzazione del contesto.</span><span class="sxs-lookup"><span data-stu-id="5e2f5-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="5e2f5-244">Il fattore di riempimento dell'indice ora può essere [configurato in SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="5e2f5-245">La nuova <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> proprietà può essere utilizzata per distinguere quando si utilizza un provider relazionale e un provider non relazionale (ad esempio InMemory).</span><span class="sxs-lookup"><span data-stu-id="5e2f5-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>
