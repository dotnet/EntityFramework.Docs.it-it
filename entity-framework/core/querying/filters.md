---
title: Filtri di query globali - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664130"
---
# <a name="global-query-filters"></a><span data-ttu-id="cfb81-102">Filtri di query globali</span><span class="sxs-lookup"><span data-stu-id="cfb81-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="cfb81-103">Questa funzionalità è stata introdotta in EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="cfb81-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="cfb81-104">I filtri di query globali sono predicati di query LINQ (un'espressione booleana in genere passata all'operatore di query *Where* di LINQ) applicati ai tipi di entità nel modello di metadati (solitamente in *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="cfb81-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="cfb81-105">Questi filtri vengono automaticamente applicati a qualsiasi query LINQ che interessa questi tipi di entità, inclusi quelli a cui si fa riferimento in modo indiretto, ad esempio tramite l'uso di riferimenti alle proprietà Include o di navigazione diretta.</span><span class="sxs-lookup"><span data-stu-id="cfb81-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="cfb81-106">Alcune applicazioni comuni di questa funzionalità sono:</span><span class="sxs-lookup"><span data-stu-id="cfb81-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="cfb81-107">**Eliminazione temporanea**, un tipo di entità definisce una proprietà *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="cfb81-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="cfb81-108">**Multi-tenant** : un tipo di entità definisce una proprietà *TenantId* .</span><span class="sxs-lookup"><span data-stu-id="cfb81-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="cfb81-109">Esempio</span><span class="sxs-lookup"><span data-stu-id="cfb81-109">Example</span></span>

<span data-ttu-id="cfb81-110">L'esempio seguente mostra come usare i filtri di query globali per implementare i comportamenti di query per eliminazione temporanea e multi-tenancy in un modello di blog semplice.</span><span class="sxs-lookup"><span data-stu-id="cfb81-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="cfb81-111">È possibile visualizzare un [esempio di multi-tenant](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) ed [esempi usando le navigazioni](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="cfb81-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="cfb81-112">Prima di tutto, definire le entità:</span><span class="sxs-lookup"><span data-stu-id="cfb81-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="cfb81-113">Si noti la dichiarazione di un campo _tenantId_ per l'entità _Blog_.</span><span class="sxs-lookup"><span data-stu-id="cfb81-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="cfb81-114">Questo verrà usato per associare ogni istanza di Blog a un tenant specifico.</span><span class="sxs-lookup"><span data-stu-id="cfb81-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="cfb81-115">Viene anche definita una proprietà _IsDeleted_ per il tipo di entità _Post_.</span><span class="sxs-lookup"><span data-stu-id="cfb81-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="cfb81-116">Questa proprietà viene usata per tenere traccia dell'eliminazione temporanea di un'istanza di _Post_.</span><span class="sxs-lookup"><span data-stu-id="cfb81-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="cfb81-117">L'istanza è contrassegnata come eliminata senza rimuovere fisicamente i dati sottostanti.</span><span class="sxs-lookup"><span data-stu-id="cfb81-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="cfb81-118">A questo punto, configurare i filtri di query in _OnModelCreating_ usando l'API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="cfb81-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="cfb81-119">Le espressioni del predicato passate alle chiamate di _HasQueryFilter_ verranno ora applicate automaticamente a tutte le query LINQ per tali tipi.</span><span class="sxs-lookup"><span data-stu-id="cfb81-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="cfb81-120">Si noti l'uso del campo a livello di istanza di DbContext `_tenantId`, usato per impostare il tenant corrente.</span><span class="sxs-lookup"><span data-stu-id="cfb81-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="cfb81-121">I filtri a livello di modello useranno il valore dell'istanza del contesto corretta, ovvero l'istanza che esegue la query.</span><span class="sxs-lookup"><span data-stu-id="cfb81-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="cfb81-122">Attualmente non è possibile definire più filtri query sulla stessa entità. verrà applicato solo l'ultimo.</span><span class="sxs-lookup"><span data-stu-id="cfb81-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="cfb81-123">Tuttavia, è possibile definire un singolo filtro con più condizioni usando l'operatore _and_ logico ([ `&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="cfb81-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="cfb81-124">Uso delle navigazioni</span><span class="sxs-lookup"><span data-stu-id="cfb81-124">Use of navigations</span></span>

<span data-ttu-id="cfb81-125">È possibile utilizzare le navigazioni durante la definizione di filtri di query globali.</span><span class="sxs-lookup"><span data-stu-id="cfb81-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="cfb81-126">Vengono applicati in modo ricorsivo: quando vengono convertite le navigazioni utilizzate nei filtri di query, vengono applicati anche i filtri di query definiti sulle entità a cui viene fatto riferimento, aggiungendo potenzialmente ulteriori spostamenti.</span><span class="sxs-lookup"><span data-stu-id="cfb81-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="cfb81-127">Attualmente EF Core non rileva i cicli nelle definizioni di filtro delle query globali, quindi è necessario prestare attenzione durante la definizione.</span><span class="sxs-lookup"><span data-stu-id="cfb81-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="cfb81-128">Se specificato in modo non corretto, questo potrebbe causare cicli infiniti durante la conversione delle query.</span><span class="sxs-lookup"><span data-stu-id="cfb81-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a><span data-ttu-id="cfb81-129">Accesso all'entità con filtro query tramite la navigazione reqiured</span><span class="sxs-lookup"><span data-stu-id="cfb81-129">Accessing entity with query filter using reqiured navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="cfb81-130">L'uso della navigazione necessaria per accedere a un'entità con un filtro di query globale definito può causare risultati imprevisti.</span><span class="sxs-lookup"><span data-stu-id="cfb81-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="cfb81-131">Per la navigazione obbligatoria si prevede che l'entità correlata sia sempre presente.</span><span class="sxs-lookup"><span data-stu-id="cfb81-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="cfb81-132">Se l'entità correlata obbligatoria viene esclusa dal filtro della query, l'entità padre potrebbe finire in uno stato imprevisto.</span><span class="sxs-lookup"><span data-stu-id="cfb81-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="cfb81-133">Questo può comportare la restituzione di un numero inferiore di elementi del previsto.</span><span class="sxs-lookup"><span data-stu-id="cfb81-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="cfb81-134">Per illustrare il problema, è possibile usare le `Blog` `Post` entità e specificate in precedenza e il metodo _OnModelCreating_ seguente:</span><span class="sxs-lookup"><span data-stu-id="cfb81-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="cfb81-135">È possibile effettuare il seeding del modello con i dati seguenti:</span><span class="sxs-lookup"><span data-stu-id="cfb81-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="cfb81-136">Il problema può essere rilevato durante l'esecuzione di due query:</span><span class="sxs-lookup"><span data-stu-id="cfb81-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="cfb81-137">Con questa configurazione, la prima query restituisce tutti i 6 oggetti `Post` , tuttavia la seconda query restituisce solo 3.</span><span class="sxs-lookup"><span data-stu-id="cfb81-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="cfb81-138">Questo problema si verifica perché il metodo _include_ nella seconda query carica le `Blog` entità correlate.</span><span class="sxs-lookup"><span data-stu-id="cfb81-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="cfb81-139">Poiché la navigazione tra `Blog` e `Post` è obbligatoria, EF core utilizza `INNER JOIN` quando si costruisce la query:</span><span class="sxs-lookup"><span data-stu-id="cfb81-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="cfb81-140">L'uso di `INNER JOIN` Filtra tutti i i `Post` cui oggetti correlati `Blog` sono stati rimossi da un filtro di query globale.</span><span class="sxs-lookup"><span data-stu-id="cfb81-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="cfb81-141">Può essere risolto usando la navigazione facoltativa anziché obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="cfb81-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="cfb81-142">In questo modo la prima query rimane invariata, ma la seconda query genera ora `LEFT JOIN` e restituisce 6 risultati.</span><span class="sxs-lookup"><span data-stu-id="cfb81-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="cfb81-143">Un approccio alternativo consiste nello specificare filtri coerenti in entrambe `Blog` le `Post` entità e.</span><span class="sxs-lookup"><span data-stu-id="cfb81-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="cfb81-144">In questo modo, i filtri corrispondenti vengono applicati sia a `Blog` che a `Post` .</span><span class="sxs-lookup"><span data-stu-id="cfb81-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="cfb81-145">`Post`gli oggetti che potrebbero finire nello stato imprevisto vengono rimossi ed entrambe le query restituiscono 3 risultati.</span><span class="sxs-lookup"><span data-stu-id="cfb81-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="cfb81-146">Disabilitazione dei filtri</span><span class="sxs-lookup"><span data-stu-id="cfb81-146">Disabling Filters</span></span>

<span data-ttu-id="cfb81-147">È possibile disabilitare i filtri per singole query LINQ usando l'operatore `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="cfb81-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="cfb81-148">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="cfb81-148">Limitations</span></span>

<span data-ttu-id="cfb81-149">I filtri di query globali presentano le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="cfb81-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="cfb81-150">I filtri possono essere definiti solo per il tipo di entità radice di una gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="cfb81-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
