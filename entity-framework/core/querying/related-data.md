---
title: Caricamento di dati correlati - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370473"
---
# <a name="loading-related-data"></a><span data-ttu-id="9e338-102">Caricamento di dati correlati</span><span class="sxs-lookup"><span data-stu-id="9e338-102">Loading Related Data</span></span>

<span data-ttu-id="9e338-103">Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate.</span><span class="sxs-lookup"><span data-stu-id="9e338-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="9e338-104">Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="9e338-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="9e338-105">**Caricamento eager** significa che i dati correlati vengono caricati dal database come parte della query iniziale.</span><span class="sxs-lookup"><span data-stu-id="9e338-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="9e338-106">**Caricamento esplicito** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="9e338-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="9e338-107">**Caricamento lazy** significa che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e338-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="9e338-108">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="9e338-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="9e338-109">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="9e338-109">Eager loading</span></span>

<span data-ttu-id="9e338-110">È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="9e338-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="9e338-111">Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.</span><span class="sxs-lookup"><span data-stu-id="9e338-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="9e338-112">Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="9e338-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="9e338-113">Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e338-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="9e338-114">È possibile includere dati correlati da più relazioni in una singola query.</span><span class="sxs-lookup"><span data-stu-id="9e338-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="9e338-115">Inclusione di più livelli</span><span class="sxs-lookup"><span data-stu-id="9e338-115">Including multiple levels</span></span>

<span data-ttu-id="9e338-116">È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="9e338-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="9e338-117">L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.</span><span class="sxs-lookup"><span data-stu-id="9e338-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="9e338-118">È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="9e338-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="9e338-119">È possibile combinare tutto ciò per includere dati correlati da più livelli e più nodi radice nella stessa query.</span><span class="sxs-lookup"><span data-stu-id="9e338-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="9e338-120">È possibile che si vogliano includere più entità correlate per una delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="9e338-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="9e338-121">Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`.</span><span class="sxs-lookup"><span data-stu-id="9e338-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="9e338-122">A tale scopo, occorre specificare ogni percorso di inclusione iniziando dalla radice.</span><span class="sxs-lookup"><span data-stu-id="9e338-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="9e338-123">Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="9e338-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="9e338-124">Questo non significa che si otterranno join ridondanti. Nella maggior parte dei casi EF consoliderà i join durante la generazione di SQL.</span><span class="sxs-lookup"><span data-stu-id="9e338-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="9e338-125">A partire dalla versione 3.0.0, ognuno di essi `Include` provocherà l'aggiunta di un join aggiuntivo alle query SQL prodotte dai provider relazionali, mentre le versioni precedenti generavano query SQL aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="9e338-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="9e338-126">Questo può modificare in modo significativo le prestazioni delle query, per un miglioramento o peggio.</span><span class="sxs-lookup"><span data-stu-id="9e338-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="9e338-127">In particolare, potrebbe essere necessario suddividere le query LINQ con un numero di operatori estremamente elevato in `Include` più query LINQ separate per evitare il problema di esplosione cartesiana.</span><span class="sxs-lookup"><span data-stu-id="9e338-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="9e338-128">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="9e338-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="9e338-129">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="9e338-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="9e338-130">Quando si applica l'inclusione per caricare dati correlati, è possibile applicare determinate operazioni enumerabili sulla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.</span><span class="sxs-lookup"><span data-stu-id="9e338-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="9e338-131">Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .</span><span class="sxs-lookup"><span data-stu-id="9e338-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="9e338-132">Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9e338-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="9e338-133">Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro.</span><span class="sxs-lookup"><span data-stu-id="9e338-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="9e338-134">Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:</span><span class="sxs-lookup"><span data-stu-id="9e338-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="9e338-135">In alternativa, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:</span><span class="sxs-lookup"><span data-stu-id="9e338-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="9e338-136">Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](tracking.md).</span><span class="sxs-lookup"><span data-stu-id="9e338-136">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="9e338-137">Tutte le entità rilevanti che sono state querried per le versioni precedenti e sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro.</span><span class="sxs-lookup"><span data-stu-id="9e338-137">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="9e338-138">Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.</span><span class="sxs-lookup"><span data-stu-id="9e338-138">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="9e338-139">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e338-139">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="9e338-140">Inclusione per i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="9e338-140">Include on derived types</span></span>

<span data-ttu-id="9e338-141">È possibile includere dati correlati dalle navigazioni definite solo per un tipo derivato mediante `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="9e338-141">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="9e338-142">Dato il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="9e338-142">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="9e338-143">Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:</span><span class="sxs-lookup"><span data-stu-id="9e338-143">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="9e338-144">Cast</span><span class="sxs-lookup"><span data-stu-id="9e338-144">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="9e338-145">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="9e338-145">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="9e338-146">Overload di `Include` che accetta parametri di tipo `string`</span><span class="sxs-lookup"><span data-stu-id="9e338-146">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="9e338-147">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="9e338-147">Explicit loading</span></span>

<span data-ttu-id="9e338-148">È possibile caricare in modo esplicito una proprietà di navigazione tramite l'API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="9e338-148">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="9e338-149">È anche possibile caricare in modo esplicito una proprietà di navigazione eseguendo una query separata che restituisce le entità correlate.</span><span class="sxs-lookup"><span data-stu-id="9e338-149">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="9e338-150">Se è abilitato il rilevamento delle modifiche, durante il caricamento di un'entità EF Core imposterà automaticamente le proprietà di navigazione dell'entità appena caricata in modo da fare riferimento alle entità già caricate e imposterà le proprietà di navigazione delle entità già caricate in modo da fare riferimento all'entità appena caricata.</span><span class="sxs-lookup"><span data-stu-id="9e338-150">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="9e338-151">Esecuzione di query su entità correlate</span><span class="sxs-lookup"><span data-stu-id="9e338-151">Querying related entities</span></span>

<span data-ttu-id="9e338-152">È anche possibile ottenere una query LINQ che rappresenta il contenuto di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e338-152">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="9e338-153">Ciò consente di eseguire operazioni quali l'esecuzione di un operatore di aggregazione sulle entità correlate senza caricarle in memoria.</span><span class="sxs-lookup"><span data-stu-id="9e338-153">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="9e338-154">È anche possibile filtrare le entità correlate che vengono caricate in memoria.</span><span class="sxs-lookup"><span data-stu-id="9e338-154">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="9e338-155">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="9e338-155">Lazy loading</span></span>

<span data-ttu-id="9e338-156">Il modo più semplice per usare il caricamento lazy consiste nell'installare il pacchetto [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitarlo con una chiamata a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="9e338-156">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="9e338-157">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9e338-157">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="9e338-158">O quando si usa AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="9e338-158">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="9e338-159">EF Core abiliterà quindi il caricamento lazy per qualsiasi proprietà di navigazione che può essere sottoposta a override, ovvero deve essere `virtual` e in una classe ereditabile.</span><span class="sxs-lookup"><span data-stu-id="9e338-159">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="9e338-160">Ad esempio, nelle entità seguenti, le proprietà di navigazione `Post.Blog` e `Blog.Posts` vengono caricate in modalità lazy.</span><span class="sxs-lookup"><span data-stu-id="9e338-160">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="9e338-161">Caricamento lazy senza proxy</span><span class="sxs-lookup"><span data-stu-id="9e338-161">Lazy loading without proxies</span></span>

<span data-ttu-id="9e338-162">I proxy di caricamento lazy operano inserendo il servizio `ILazyLoader` in un'entità, come descritto in [Costruttori di tipi di entità](../modeling/constructors.md).</span><span class="sxs-lookup"><span data-stu-id="9e338-162">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="9e338-163">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9e338-163">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="9e338-164">In questo caso non è richiesto che i tipi di entità vengano ereditati o che le proprietà di navigazione siano virtuali e le istanze di entità possono essere create con `new` per eseguire il caricamento lazy dopo il collegamento a un contesto.</span><span class="sxs-lookup"><span data-stu-id="9e338-164">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="9e338-165">Tuttavia, è necessario un riferimento al servizio `ILazyLoader`, che viene definito nel pacchetto [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="9e338-165">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="9e338-166">Questo pacchetto contiene un set minimo di tipi in modo che vi sia un impatto minimo per le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9e338-166">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="9e338-167">Tuttavia, per evitare completamente di dipendere da pacchetti di EF Core nei tipi di entità, è possibile inserire il metodo `ILazyLoader.Load` come delegato.</span><span class="sxs-lookup"><span data-stu-id="9e338-167">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="9e338-168">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9e338-168">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="9e338-169">Il codice precedente usa un metodo di estensione `Load` per chiarire l'uso del delegato:</span><span class="sxs-lookup"><span data-stu-id="9e338-169">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="9e338-170">Il parametro del costruttore per il delegato di caricamento lazy deve essere chiamato "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="9e338-170">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="9e338-171">La possibilità di configurare l'uso di un nome diverso è pianificata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="9e338-171">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="9e338-172">Dati correlati e serializzazione</span><span class="sxs-lookup"><span data-stu-id="9e338-172">Related data and serialization</span></span>

<span data-ttu-id="9e338-173">Dato che EF Core correggerà automaticamente le proprietà di navigazione, è possibile che il grafo degli oggetti contenga cicli.</span><span class="sxs-lookup"><span data-stu-id="9e338-173">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="9e338-174">Ad esempio, il caricamento di un blog e dei post correlati risulterà in un oggetto blog che fa riferimento a una raccolta di post.</span><span class="sxs-lookup"><span data-stu-id="9e338-174">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="9e338-175">Ognuno di tali post avrà un riferimento al blog.</span><span class="sxs-lookup"><span data-stu-id="9e338-175">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="9e338-176">Alcuni framework di serializzazione non consentono tali cicli.</span><span class="sxs-lookup"><span data-stu-id="9e338-176">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="9e338-177">Ad esempio, Json.NET genererà l'eccezione seguente se viene rilevato un ciclo.</span><span class="sxs-lookup"><span data-stu-id="9e338-177">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="9e338-178">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: ciclo autoreferenziale rilevato per la proprietà 'Blog' con tipo 'MyApplication.Models.Blog')</span><span class="sxs-lookup"><span data-stu-id="9e338-178">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="9e338-179">Se si usa ASP.NET Core, è possibile configurare Json.NET per ignorare i cicli trovati nel grafo degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="9e338-179">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="9e338-180">Questa operazione viene eseguita nel metodo `ConfigureServices(...)` in `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="9e338-180">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="9e338-181">In alternativa è possibile aggiungere l'attributo `[JsonIgnore]` a una proprietà di navigazione, per indicare a Json.NET di non attraversare la proprietà di navigazione durante la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="9e338-181">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
