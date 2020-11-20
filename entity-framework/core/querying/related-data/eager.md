---
title: Caricamento eager dei dati correlati-EF Core
description: Caricamento eager di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003575"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="24c96-103">Caricamento eager di dati correlati</span><span class="sxs-lookup"><span data-stu-id="24c96-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="24c96-104">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="24c96-104">Eager loading</span></span>

<span data-ttu-id="24c96-105">È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="24c96-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="24c96-106">Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.</span><span class="sxs-lookup"><span data-stu-id="24c96-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="24c96-107">Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="24c96-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="24c96-108">Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="24c96-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="24c96-109">È possibile includere dati correlati da più relazioni in una singola query.</span><span class="sxs-lookup"><span data-stu-id="24c96-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="24c96-110">Il caricamento eager di un'esplorazione della raccolta in una singola query può causare problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="24c96-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="24c96-111">Per altre informazioni, vedere [confronto tra query singole e Split](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="24c96-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="24c96-112">Inclusione di più livelli</span><span class="sxs-lookup"><span data-stu-id="24c96-112">Including multiple levels</span></span>

<span data-ttu-id="24c96-113">È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="24c96-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="24c96-114">L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.</span><span class="sxs-lookup"><span data-stu-id="24c96-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="24c96-115">È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="24c96-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="24c96-116">È possibile combinare tutte le chiamate per includere dati correlati da più livelli e più radici nella stessa query.</span><span class="sxs-lookup"><span data-stu-id="24c96-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="24c96-117">È possibile che si vogliano includere più entità correlate per una delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="24c96-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="24c96-118">Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`.</span><span class="sxs-lookup"><span data-stu-id="24c96-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="24c96-119">Per includere entrambi, è necessario specificare ogni percorso di inclusione a partire dalla radice.</span><span class="sxs-lookup"><span data-stu-id="24c96-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="24c96-120">Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="24c96-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="24c96-121">Non significa che si otterranno join ridondanti; nella maggior parte dei casi, EF combinerà i join durante la generazione di SQL.</span><span class="sxs-lookup"><span data-stu-id="24c96-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> <span data-ttu-id="24c96-122">È anche possibile caricare più navigazioni usando un solo `Include` metodo.</span><span class="sxs-lookup"><span data-stu-id="24c96-122">You can also load multiple navigations using a single `Include` method.</span></span> <span data-ttu-id="24c96-123">Questo è possibile per la navigazione "Chains" che sono tutti riferimenti o quando terminano con una singola raccolta.</span><span class="sxs-lookup"><span data-stu-id="24c96-123">This is possible for navigation "chains" that are all references, or when they end with a single collection.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a><span data-ttu-id="24c96-124">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="24c96-124">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="24c96-125">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="24c96-125">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="24c96-126">Quando si applica l'inclusione per caricare dati correlati, è possibile aggiungere determinate operazioni enumerabili alla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.</span><span class="sxs-lookup"><span data-stu-id="24c96-126">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="24c96-127">Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .</span><span class="sxs-lookup"><span data-stu-id="24c96-127">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="24c96-128">Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="24c96-128">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="24c96-129">Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro.</span><span class="sxs-lookup"><span data-stu-id="24c96-129">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="24c96-130">Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:</span><span class="sxs-lookup"><span data-stu-id="24c96-130">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="24c96-131">Al contrario, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:</span><span class="sxs-lookup"><span data-stu-id="24c96-131">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="24c96-132">Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="24c96-132">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="24c96-133">Tutte le entità rilevanti su cui sono state eseguite query in precedenza e che sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro.</span><span class="sxs-lookup"><span data-stu-id="24c96-133">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="24c96-134">Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.</span><span class="sxs-lookup"><span data-stu-id="24c96-134">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="24c96-135">Esempio:</span><span class="sxs-lookup"><span data-stu-id="24c96-135">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="24c96-136">In caso di rilevamento di query, viene considerata caricata la navigazione su cui è stato applicato l'inclusione filtrato.</span><span class="sxs-lookup"><span data-stu-id="24c96-136">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="24c96-137">Ciò significa che EF Core non tenterà di ricaricare i valori usando il caricamento [esplicito](xref:core/querying/related-data/explicit) o il [caricamento lazy](xref:core/querying/related-data/lazy), anche se alcuni elementi potrebbero ancora essere mancanti.</span><span class="sxs-lookup"><span data-stu-id="24c96-137">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="24c96-138">Inclusione per i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="24c96-138">Include on derived types</span></span>

<span data-ttu-id="24c96-139">È possibile includere dati correlati dalla navigazione definita solo in un tipo derivato usando `Include` e `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="24c96-139">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="24c96-140">Dato il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="24c96-140">Given the following model:</span></span>

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

<span data-ttu-id="24c96-141">Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:</span><span class="sxs-lookup"><span data-stu-id="24c96-141">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="24c96-142">Cast</span><span class="sxs-lookup"><span data-stu-id="24c96-142">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="24c96-143">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="24c96-143">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="24c96-144">Overload di `Include` che accetta parametri di tipo `string`</span><span class="sxs-lookup"><span data-stu-id="24c96-144">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
