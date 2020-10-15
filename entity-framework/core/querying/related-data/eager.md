---
title: Caricamento eager dei dati correlati-EF Core
description: Caricamento eager di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062576"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="4ba4b-103">Caricamento eager di dati correlati</span><span class="sxs-lookup"><span data-stu-id="4ba4b-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="4ba4b-104">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="4ba4b-104">Eager loading</span></span>

<span data-ttu-id="4ba4b-105">È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="4ba4b-106">Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="4ba4b-107">Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="4ba4b-108">Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="4ba4b-109">È possibile includere dati correlati da più relazioni in una singola query.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="4ba4b-110">Inclusione di più livelli</span><span class="sxs-lookup"><span data-stu-id="4ba4b-110">Including multiple levels</span></span>

<span data-ttu-id="4ba4b-111">È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="4ba4b-112">L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="4ba4b-113">È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="4ba4b-114">È possibile combinare tutte le chiamate per includere dati correlati da più livelli e più radici nella stessa query.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="4ba4b-115">È possibile che si vogliano includere più entità correlate per una delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="4ba4b-116">Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="4ba4b-117">Per includere entrambi, è necessario specificare ogni percorso di inclusione a partire dalla radice.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="4ba4b-118">Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="4ba4b-119">Non significa che si otterranno join ridondanti; nella maggior parte dei casi, EF combinerà i join durante la generazione di SQL.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="4ba4b-120">Query single e Split</span><span class="sxs-lookup"><span data-stu-id="4ba4b-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="4ba4b-121">Query singole</span><span class="sxs-lookup"><span data-stu-id="4ba4b-121">Single queries</span></span>

<span data-ttu-id="4ba4b-122">Nei database relazionali, per impostazione predefinita, tutte le entità correlate vengono caricate introducendo i JOIN:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="4ba4b-123">Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog, causando il cosiddetto problema "esplosione cartesiana".</span><span class="sxs-lookup"><span data-stu-id="4ba4b-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="4ba4b-124">Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="4ba4b-125">Per impostazione predefinita, EF Core genera un avviso se rileva le query che caricano la raccolta, incluse le quali possono causare problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="4ba4b-126">Suddividere query</span><span class="sxs-lookup"><span data-stu-id="4ba4b-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="4ba4b-127">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="4ba4b-128">EF consente di specificare che una query LINQ specificata deve essere *suddivisa* in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="4ba4b-129">Anziché JOIN, le query suddivise eseguono una query SQL aggiuntiva per ogni spostamento uno-a-molti incluso:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="4ba4b-130">Verrà generato il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-130">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="4ba4b-131">Le entità correlate uno-a-uno vengono sempre caricate tramite JOIN nella stessa query, in quanto non ha alcun effetto sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="4ba4b-132">Abilitazione di query Split a livello globale</span><span class="sxs-lookup"><span data-stu-id="4ba4b-132">Enabling split queries globally</span></span>

<span data-ttu-id="4ba4b-133">È anche possibile configurare le query Split come predefinite per il contesto dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="4ba4b-134">Quando le query suddivise sono configurate come predefinite, è comunque possibile configurare query specifiche da eseguire come query singole:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="4ba4b-135">Se la modalità di suddivisione delle query non è specificata in modo esplicito, né globalmente né nella query, né EF Core rileva che una singola query carica più inclusioni di raccolta, viene generato un avviso per attirare l'attenzione sui potenziali problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="4ba4b-136">Se si imposta la modalità di query su SingleQuery, l'avviso non verrà generato.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="4ba4b-137">Caratteristiche delle query suddivise</span><span class="sxs-lookup"><span data-stu-id="4ba4b-137">Characteristics of split queries</span></span>

<span data-ttu-id="4ba4b-138">Mentre Split query evita i problemi di prestazioni associati a JOIN e l'esplosione cartesiana, presenta anche alcuni svantaggi:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="4ba4b-139">Anche se la maggior parte dei database garantisce la coerenza dei dati per le singole query, non esistono garanzie di questo tipo per più query.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="4ba4b-140">Se il database viene aggiornato simultaneamente durante l'esecuzione delle query, i dati risultanti potrebbero non essere coerenti.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="4ba4b-141">È possibile mitigarlo eseguendo il wrapping delle query in una transazione serializzabile o snapshot, anche se in questo modo è possibile creare problemi di prestazioni propri.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="4ba4b-142">Per ulteriori informazioni, vedere la documentazione del database.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="4ba4b-143">Ogni query implica attualmente un round trip di rete aggiuntivo al database.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="4ba4b-144">Il round trip della rete può compromettere le prestazioni, soprattutto se la latenza del database è elevata, ad esempio servizi cloud.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="4ba4b-145">Sebbene alcuni database consentano di usare contemporaneamente i risultati di più query (SQL Server con MARS, SQLite), la maggior parte consente di rendere attiva una sola query in un determinato punto.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="4ba4b-146">Tutti i risultati delle query precedenti devono quindi essere memorizzati nel buffer nella memoria dell'applicazione prima di eseguire query successive, il che comporta un aumento dei requisiti di memoria.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="4ba4b-147">Sfortunatamente, non esiste una strategia per il caricamento di entità correlate che soddisfino tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="4ba4b-148">Valutare con attenzione i vantaggi e gli svantaggi delle query singole e divise e selezionare quello che soddisfa le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="4ba4b-149">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="4ba4b-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="4ba4b-150">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="4ba4b-151">Quando si applica l'inclusione per caricare dati correlati, è possibile applicare determinate operazioni enumerabili sulla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="4ba4b-152">Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .</span><span class="sxs-lookup"><span data-stu-id="4ba4b-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="4ba4b-153">Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="4ba4b-154">Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="4ba4b-155">Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="4ba4b-156">Al contrario, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="4ba4b-157">Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="4ba4b-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="4ba4b-158">Tutte le entità rilevanti su cui sono state eseguite query in precedenza e che sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="4ba4b-159">Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.</span><span class="sxs-lookup"><span data-stu-id="4ba4b-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="4ba4b-160">Esempio:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="4ba4b-161">Inclusione per i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="4ba4b-161">Include on derived types</span></span>

<span data-ttu-id="4ba4b-162">È possibile includere dati correlati dalla navigazione definita solo in un tipo derivato usando `Include` e `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="4ba4b-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="4ba4b-163">Dato il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-163">Given the following model:</span></span>

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

<span data-ttu-id="4ba4b-164">Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:</span><span class="sxs-lookup"><span data-stu-id="4ba4b-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="4ba4b-165">Cast</span><span class="sxs-lookup"><span data-stu-id="4ba4b-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="4ba4b-166">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="4ba4b-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="4ba4b-167">Overload di `Include` che accetta parametri di tipo `string`</span><span class="sxs-lookup"><span data-stu-id="4ba4b-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
