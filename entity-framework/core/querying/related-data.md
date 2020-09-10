---
title: Caricamento di dati correlati - EF Core
description: Diverse strategie per il caricamento di dati correlati con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 43b8cbac1e36a37bc85c953319407b3814d7d327
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618913"
---
# <a name="loading-related-data"></a><span data-ttu-id="66312-103">Caricamento di dati correlati</span><span class="sxs-lookup"><span data-stu-id="66312-103">Loading Related Data</span></span>

<span data-ttu-id="66312-104">Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate.</span><span class="sxs-lookup"><span data-stu-id="66312-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="66312-105">Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="66312-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="66312-106">**Caricamento eager** significa che i dati correlati vengono caricati dal database come parte della query iniziale.</span><span class="sxs-lookup"><span data-stu-id="66312-106">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="66312-107">**Caricamento esplicito** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="66312-107">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="66312-108">**Caricamento lazy** significa che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="66312-108">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="66312-109">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="66312-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="66312-110">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="66312-110">Eager loading</span></span>

<span data-ttu-id="66312-111">È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="66312-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="66312-112">Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.</span><span class="sxs-lookup"><span data-stu-id="66312-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="66312-113">Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="66312-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="66312-114">Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="66312-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="66312-115">È possibile includere dati correlati da più relazioni in una singola query.</span><span class="sxs-lookup"><span data-stu-id="66312-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="66312-116">Inclusione di più livelli</span><span class="sxs-lookup"><span data-stu-id="66312-116">Including multiple levels</span></span>

<span data-ttu-id="66312-117">È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="66312-117">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="66312-118">L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.</span><span class="sxs-lookup"><span data-stu-id="66312-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="66312-119">È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="66312-119">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="66312-120">È possibile combinare tutte le chiamate per includere dati correlati da più livelli e più radici nella stessa query.</span><span class="sxs-lookup"><span data-stu-id="66312-120">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="66312-121">È possibile che si vogliano includere più entità correlate per una delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="66312-121">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="66312-122">Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`.</span><span class="sxs-lookup"><span data-stu-id="66312-122">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="66312-123">Per includere entrambi, è necessario specificare ogni percorso di inclusione a partire dalla radice.</span><span class="sxs-lookup"><span data-stu-id="66312-123">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="66312-124">Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="66312-124">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="66312-125">Non significa che si otterranno join ridondanti; nella maggior parte dei casi, EF combinerà i join durante la generazione di SQL.</span><span class="sxs-lookup"><span data-stu-id="66312-125">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="66312-126">Query single e Split</span><span class="sxs-lookup"><span data-stu-id="66312-126">Single and split queries</span></span>

#### <a name="single-queries"></a><span data-ttu-id="66312-127">Query singole</span><span class="sxs-lookup"><span data-stu-id="66312-127">Single queries</span></span>

<span data-ttu-id="66312-128">Nei database relazionali, per impostazione predefinita, tutte le entità correlate vengono caricate introducendo i JOIN:</span><span class="sxs-lookup"><span data-stu-id="66312-128">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="66312-129">Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog, causando il cosiddetto problema "esplosione cartesiana".</span><span class="sxs-lookup"><span data-stu-id="66312-129">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="66312-130">Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="66312-130">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="66312-131">Per impostazione predefinita, EF Core genera un avviso se rileva le query che caricano la raccolta, incluse le quali possono causare problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="66312-131">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

#### <a name="split-queries"></a><span data-ttu-id="66312-132">Suddividere query</span><span class="sxs-lookup"><span data-stu-id="66312-132">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="66312-133">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="66312-133">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="66312-134">EF consente di specificare che una query LINQ specificata deve essere *suddivisa* in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="66312-134">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="66312-135">Anziché JOIN, le query suddivise eseguono una query SQL aggiuntiva per ogni spostamento uno-a-molti incluso:</span><span class="sxs-lookup"><span data-stu-id="66312-135">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="66312-136">Verrà generato il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="66312-136">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="66312-137">Le entità correlate uno-a-uno vengono sempre caricate tramite JOIN nella stessa query, in quanto non ha alcun effetto sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="66312-137">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

#### <a name="enabling-split-queries-globally"></a><span data-ttu-id="66312-138">Abilitazione di query Split a livello globale</span><span class="sxs-lookup"><span data-stu-id="66312-138">Enabling split queries globally</span></span>

<span data-ttu-id="66312-139">È anche possibile configurare le query Split come predefinite per il contesto dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="66312-139">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="66312-140">Quando le query suddivise sono configurate come predefinite, è comunque possibile configurare query specifiche da eseguire come query singole:</span><span class="sxs-lookup"><span data-stu-id="66312-140">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="66312-141">Se la modalità di suddivisione delle query non è specificata in modo esplicito, né globalmente né nella query, né EF Core rileva che una singola query carica più inclusioni di raccolta, viene generato un avviso per attirare l'attenzione sui potenziali problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="66312-141">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="66312-142">Se si imposta la modalità di query su SingleQuery, l'avviso non verrà generato.</span><span class="sxs-lookup"><span data-stu-id="66312-142">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

#### <a name="characteristics-of-split-queries"></a><span data-ttu-id="66312-143">Caratteristiche delle query suddivise</span><span class="sxs-lookup"><span data-stu-id="66312-143">Characteristics of split queries</span></span>

<span data-ttu-id="66312-144">Mentre Split query evita i problemi di prestazioni associati a JOIN e l'esplosione cartesiana, presenta anche alcuni svantaggi:</span><span class="sxs-lookup"><span data-stu-id="66312-144">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="66312-145">Anche se la maggior parte dei database garantisce la coerenza dei dati per le singole query, non esistono garanzie di questo tipo per più query.</span><span class="sxs-lookup"><span data-stu-id="66312-145">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="66312-146">Se il database viene aggiornato simultaneamente durante l'esecuzione delle query, i dati risultanti potrebbero non essere coerenti.</span><span class="sxs-lookup"><span data-stu-id="66312-146">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="66312-147">È possibile mitigarlo eseguendo il wrapping delle query in una transazione serializzabile o snapshot, anche se in questo modo è possibile creare problemi di prestazioni propri.</span><span class="sxs-lookup"><span data-stu-id="66312-147">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="66312-148">Per ulteriori informazioni, vedere la documentazione del database.</span><span class="sxs-lookup"><span data-stu-id="66312-148">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="66312-149">Ogni query implica attualmente un round trip di rete aggiuntivo al database.</span><span class="sxs-lookup"><span data-stu-id="66312-149">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="66312-150">Il round trip della rete può compromettere le prestazioni, soprattutto se la latenza del database è elevata, ad esempio servizi cloud.</span><span class="sxs-lookup"><span data-stu-id="66312-150">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="66312-151">Sebbene alcuni database consentano di usare contemporaneamente i risultati di più query (SQL Server con MARS, SQLite), la maggior parte consente di rendere attiva una sola query in un determinato punto.</span><span class="sxs-lookup"><span data-stu-id="66312-151">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="66312-152">Tutti i risultati delle query precedenti devono quindi essere memorizzati nel buffer nella memoria dell'applicazione prima di eseguire query successive, il che comporta un aumento dei requisiti di memoria.</span><span class="sxs-lookup"><span data-stu-id="66312-152">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="66312-153">Sfortunatamente, non esiste una strategia per il caricamento di entità correlate che soddisfino tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="66312-153">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="66312-154">Valutare con attenzione i vantaggi e gli svantaggi delle query singole e divise e selezionare quello che soddisfa le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="66312-154">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="66312-155">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="66312-155">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="66312-156">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="66312-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="66312-157">Quando si applica l'inclusione per caricare dati correlati, è possibile applicare determinate operazioni enumerabili sulla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.</span><span class="sxs-lookup"><span data-stu-id="66312-157">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="66312-158">Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .</span><span class="sxs-lookup"><span data-stu-id="66312-158">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="66312-159">Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="66312-159">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="66312-160">Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro.</span><span class="sxs-lookup"><span data-stu-id="66312-160">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="66312-161">Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:</span><span class="sxs-lookup"><span data-stu-id="66312-161">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="66312-162">Al contrario, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:</span><span class="sxs-lookup"><span data-stu-id="66312-162">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="66312-163">Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="66312-163">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="66312-164">Tutte le entità rilevanti su cui sono state eseguite query in precedenza e che sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro.</span><span class="sxs-lookup"><span data-stu-id="66312-164">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="66312-165">Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.</span><span class="sxs-lookup"><span data-stu-id="66312-165">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="66312-166">Esempio:</span><span class="sxs-lookup"><span data-stu-id="66312-166">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="66312-167">Inclusione per i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="66312-167">Include on derived types</span></span>

<span data-ttu-id="66312-168">È possibile includere dati correlati dalla navigazione definita solo in un tipo derivato usando `Include` e `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="66312-168">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="66312-169">Dato il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="66312-169">Given the following model:</span></span>

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

<span data-ttu-id="66312-170">Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:</span><span class="sxs-lookup"><span data-stu-id="66312-170">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="66312-171">Cast</span><span class="sxs-lookup"><span data-stu-id="66312-171">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="66312-172">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="66312-172">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="66312-173">Overload di `Include` che accetta parametri di tipo `string`</span><span class="sxs-lookup"><span data-stu-id="66312-173">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="66312-174">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="66312-174">Explicit loading</span></span>

<span data-ttu-id="66312-175">È possibile caricare in modo esplicito una proprietà di navigazione tramite l'API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="66312-175">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="66312-176">È anche possibile caricare in modo esplicito una proprietà di navigazione eseguendo una query separata che restituisce le entità correlate.</span><span class="sxs-lookup"><span data-stu-id="66312-176">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="66312-177">Se il rilevamento delle modifiche è abilitato, quando la query materializza un'entità, EF Core imposterà automaticamente le proprietà di navigazione dell'entità appena caricata in modo da fare riferimento alle entità già caricate e impostare le proprietà di navigazione delle entità già caricate in modo che facciano riferimento all'entità appena caricata.</span><span class="sxs-lookup"><span data-stu-id="66312-177">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="66312-178">Esecuzione di query su entità correlate</span><span class="sxs-lookup"><span data-stu-id="66312-178">Querying related entities</span></span>

<span data-ttu-id="66312-179">È anche possibile ottenere una query LINQ che rappresenta il contenuto di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="66312-179">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="66312-180">Consente di applicare operatori aggiuntivi alla query.</span><span class="sxs-lookup"><span data-stu-id="66312-180">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="66312-181">Esempio di nemico che applica un operatore di aggregazione sulle entità correlate senza caricarli in memoria.</span><span class="sxs-lookup"><span data-stu-id="66312-181">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="66312-182">È anche possibile filtrare le entità correlate che vengono caricate in memoria.</span><span class="sxs-lookup"><span data-stu-id="66312-182">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="66312-183">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="66312-183">Lazy loading</span></span>

<span data-ttu-id="66312-184">Il modo più semplice per usare il caricamento lazy consiste nell'installare il pacchetto [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitarlo con una chiamata a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="66312-184">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="66312-185">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="66312-185">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="66312-186">O quando si usa AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="66312-186">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="66312-187">EF Core abiliterà quindi il caricamento lazy per qualsiasi proprietà di navigazione che può essere sottoposta a override, ovvero deve essere `virtual` e in una classe ereditabile.</span><span class="sxs-lookup"><span data-stu-id="66312-187">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="66312-188">Ad esempio, nelle entità seguenti, le proprietà di navigazione `Post.Blog` e `Blog.Posts` vengono caricate in modalità lazy.</span><span class="sxs-lookup"><span data-stu-id="66312-188">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

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

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="66312-189">Caricamento lazy senza proxy</span><span class="sxs-lookup"><span data-stu-id="66312-189">Lazy loading without proxies</span></span>

<span data-ttu-id="66312-190">I proxy di caricamento lazy operano inserendo il servizio `ILazyLoader` in un'entità, come descritto in [Costruttori di tipi di entità](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="66312-190">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="66312-191">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="66312-191">For example:</span></span>

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

<span data-ttu-id="66312-192">Questo metodo non richiede che i tipi di entità vengano ereditati dalle proprietà di navigazione o che siano virtuali e consente alle istanze di entità create con `new` il caricamento lazy una volta associate a un contesto.</span><span class="sxs-lookup"><span data-stu-id="66312-192">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="66312-193">Tuttavia, è necessario un riferimento al servizio `ILazyLoader`, che viene definito nel pacchetto [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="66312-193">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="66312-194">Questo pacchetto contiene un set minimo di tipi in modo che non vi sia un piccolo effetto in a seconda di esso.</span><span class="sxs-lookup"><span data-stu-id="66312-194">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="66312-195">Tuttavia, per evitare completamente a seconda dei pacchetti EF Core nei tipi di entità, è possibile inserire il `ILazyLoader.Load` metodo come delegato.</span><span class="sxs-lookup"><span data-stu-id="66312-195">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="66312-196">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="66312-196">For example:</span></span>

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

<span data-ttu-id="66312-197">Il codice precedente usa un metodo di estensione `Load` per chiarire l'uso del delegato:</span><span class="sxs-lookup"><span data-stu-id="66312-197">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

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
> <span data-ttu-id="66312-198">Il parametro del costruttore per il delegato di caricamento lazy deve essere chiamato "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="66312-198">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="66312-199">La possibilità di configurare l'uso di un nome diverso è pianificata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="66312-199">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="66312-200">Dati correlati e serializzazione</span><span class="sxs-lookup"><span data-stu-id="66312-200">Related data and serialization</span></span>

<span data-ttu-id="66312-201">Poiché EF Core corregge automaticamente le proprietà di navigazione, è possibile finire con i cicli nell'oggetto grafico.</span><span class="sxs-lookup"><span data-stu-id="66312-201">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="66312-202">Ad esempio, il caricamento di un blog e dei post correlati risulterà in un oggetto blog che fa riferimento a una raccolta di post.</span><span class="sxs-lookup"><span data-stu-id="66312-202">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="66312-203">Ognuno di tali post avrà un riferimento al blog.</span><span class="sxs-lookup"><span data-stu-id="66312-203">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="66312-204">Alcuni framework di serializzazione non consentono tali cicli.</span><span class="sxs-lookup"><span data-stu-id="66312-204">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="66312-205">Se, ad esempio, viene trovato un ciclo, Json.NET genererà l'eccezione seguente.</span><span class="sxs-lookup"><span data-stu-id="66312-205">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="66312-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: ciclo autoreferenziale rilevato per la proprietà 'Blog' con tipo 'MyApplication.Models.Blog')</span><span class="sxs-lookup"><span data-stu-id="66312-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="66312-207">Se si usa ASP.NET Core, è possibile configurare Json.NET in modo da ignorare i cicli trovati nell'oggetto grafico.</span><span class="sxs-lookup"><span data-stu-id="66312-207">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="66312-208">Questa configurazione viene eseguita nel `ConfigureServices(...)` metodo in `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="66312-208">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="66312-209">In alternativa è possibile aggiungere l'attributo `[JsonIgnore]` a una proprietà di navigazione, per indicare a Json.NET di non attraversare la proprietà di navigazione durante la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="66312-209">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
