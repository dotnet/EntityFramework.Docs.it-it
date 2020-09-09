---
title: Filtri di query globali - EF Core
description: Uso di filtri di query globali per filtrare i risultati con Entity Framework Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616394"
---
# <a name="global-query-filters"></a><span data-ttu-id="57b40-103">Filtri di query globali</span><span class="sxs-lookup"><span data-stu-id="57b40-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="57b40-104">Questa funzionalità è stata introdotta in EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="57b40-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="57b40-105">I filtri di query globali sono predicati di query LINQ applicati ai tipi di entità nel modello di metadati (in genere in *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="57b40-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="57b40-106">Un predicato di query è un'espressione booleana che in genere viene passata all'operatore di query *where* di LINQ.</span><span class="sxs-lookup"><span data-stu-id="57b40-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="57b40-107">EF Core applica automaticamente tali filtri alle query LINQ che coinvolgono tali tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="57b40-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="57b40-108">EF Core li applica anche ai tipi di entità, a cui viene fatto riferimento indirettamente tramite l'utilizzo della proprietà di navigazione o di inclusione.</span><span class="sxs-lookup"><span data-stu-id="57b40-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="57b40-109">Alcune applicazioni comuni di questa funzionalità sono:</span><span class="sxs-lookup"><span data-stu-id="57b40-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="57b40-110">**Eliminazione temporanea**, un tipo di entità definisce una proprietà *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="57b40-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="57b40-111">**Multi-tenant** : un tipo di entità definisce una proprietà *TenantId* .</span><span class="sxs-lookup"><span data-stu-id="57b40-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="57b40-112">Esempio</span><span class="sxs-lookup"><span data-stu-id="57b40-112">Example</span></span>

<span data-ttu-id="57b40-113">Nell'esempio seguente viene illustrato come utilizzare i filtri di query globali per implementare comportamenti di query di multi-tenant e di eliminazione temporanea in un semplice modello di Blog.</span><span class="sxs-lookup"><span data-stu-id="57b40-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="57b40-114">È possibile visualizzare un [esempio di multi-tenant](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) ed [esempi usando le navigazioni](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="57b40-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="57b40-115">Prima di tutto, definire le entità:</span><span class="sxs-lookup"><span data-stu-id="57b40-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="57b40-116">Si noti la dichiarazione di un campo _tenantId_ per l'entità _Blog_.</span><span class="sxs-lookup"><span data-stu-id="57b40-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="57b40-117">Questo campo verrà usato per associare ogni istanza di Blog a un tenant specifico.</span><span class="sxs-lookup"><span data-stu-id="57b40-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="57b40-118">Viene anche definita una proprietà _IsDeleted_ per il tipo di entità _Post_.</span><span class="sxs-lookup"><span data-stu-id="57b40-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="57b40-119">Questa proprietà viene usata per tenere traccia del fatto che un'istanza _post_ è stata "eliminata temporaneamente".</span><span class="sxs-lookup"><span data-stu-id="57b40-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="57b40-120">L'istanza è contrassegnata come eliminata senza rimuovere fisicamente i dati sottostanti.</span><span class="sxs-lookup"><span data-stu-id="57b40-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="57b40-121">A questo punto, configurare i filtri di query in _OnModelCreating_ usando l'API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="57b40-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="57b40-122">Le espressioni del predicato passate alle chiamate di _HasQueryFilter_ verranno ora applicate automaticamente a tutte le query LINQ per tali tipi.</span><span class="sxs-lookup"><span data-stu-id="57b40-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="57b40-123">Si noti l'uso del campo a livello di istanza di DbContext `_tenantId`, usato per impostare il tenant corrente.</span><span class="sxs-lookup"><span data-stu-id="57b40-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="57b40-124">I filtri a livello di modello useranno il valore dell'istanza del contesto corretta, ovvero l'istanza che esegue la query.</span><span class="sxs-lookup"><span data-stu-id="57b40-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="57b40-125">Attualmente non è possibile definire più filtri query sulla stessa entità. verrà applicato solo l'ultimo.</span><span class="sxs-lookup"><span data-stu-id="57b40-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="57b40-126">Tuttavia, è possibile definire un singolo filtro con più condizioni usando l'operatore _and_ logico ([ `&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="57b40-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="57b40-127">Uso delle navigazioni</span><span class="sxs-lookup"><span data-stu-id="57b40-127">Use of navigations</span></span>

<span data-ttu-id="57b40-128">È inoltre possibile utilizzare le navigazioni nella definizione dei filtri di query globali.</span><span class="sxs-lookup"><span data-stu-id="57b40-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="57b40-129">Se si utilizzano le navigazioni in filtro query, i filtri query verranno applicati in modo ricorsivo.</span><span class="sxs-lookup"><span data-stu-id="57b40-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="57b40-130">Quando EF Core espande le navigazioni utilizzate nei filtri di query, verranno applicati anche i filtri di query definiti sulle entità a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="57b40-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="57b40-131">Attualmente EF Core non rileva i cicli nelle definizioni di filtro delle query globali, quindi è necessario prestare attenzione durante la definizione.</span><span class="sxs-lookup"><span data-stu-id="57b40-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="57b40-132">Se specificato in modo non corretto, i cicli potrebbero causare cicli infiniti durante la conversione delle query.</span><span class="sxs-lookup"><span data-stu-id="57b40-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="57b40-133">Accesso all'entità con filtro query tramite la navigazione obbligatoria</span><span class="sxs-lookup"><span data-stu-id="57b40-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="57b40-134">L'uso della navigazione necessaria per accedere a un'entità con un filtro di query globale definito può causare risultati imprevisti.</span><span class="sxs-lookup"><span data-stu-id="57b40-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="57b40-135">Per la navigazione obbligatoria si prevede che l'entità correlata sia sempre presente.</span><span class="sxs-lookup"><span data-stu-id="57b40-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="57b40-136">Se l'entità correlata necessaria viene esclusa dal filtro di query, l'entità padre non sarà nel risultato.</span><span class="sxs-lookup"><span data-stu-id="57b40-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="57b40-137">Quindi, è possibile ottenere meno elementi del previsto.</span><span class="sxs-lookup"><span data-stu-id="57b40-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="57b40-138">Per illustrare il problema, è possibile usare le `Blog` `Post` entità e specificate in precedenza e il metodo _OnModelCreating_ seguente:</span><span class="sxs-lookup"><span data-stu-id="57b40-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="57b40-139">È possibile effettuare il seeding del modello con i dati seguenti:</span><span class="sxs-lookup"><span data-stu-id="57b40-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="57b40-140">Il problema può essere rilevato durante l'esecuzione di due query:</span><span class="sxs-lookup"><span data-stu-id="57b40-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="57b40-141">Con l'installazione precedente, la prima query restituisce tutti i 6 `Post` , tuttavia la seconda query restituisce solo 3.</span><span class="sxs-lookup"><span data-stu-id="57b40-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="57b40-142">Questa mancata corrispondenza si verifica perché il metodo di _inclusione_ nella seconda query carica le `Blog` entità correlate.</span><span class="sxs-lookup"><span data-stu-id="57b40-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="57b40-143">Poiché la navigazione tra `Blog` e `Post` è obbligatoria, EF core utilizza `INNER JOIN` quando si costruisce la query:</span><span class="sxs-lookup"><span data-stu-id="57b40-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="57b40-144">L'uso di `INNER JOIN` Filtra tutti i i `Post` cui oggetti correlati `Blog` sono stati rimossi da un filtro di query globale.</span><span class="sxs-lookup"><span data-stu-id="57b40-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="57b40-145">Può essere risolto usando la navigazione facoltativa anziché obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="57b40-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="57b40-146">In questo modo la prima query rimane invariata, ma la seconda query genera ora `LEFT JOIN` e restituisce 6 risultati.</span><span class="sxs-lookup"><span data-stu-id="57b40-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="57b40-147">Un approccio alternativo consiste nello specificare filtri coerenti in entrambe `Blog` le `Post` entità e.</span><span class="sxs-lookup"><span data-stu-id="57b40-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="57b40-148">In questo modo, i filtri corrispondenti vengono applicati sia a `Blog` che a `Post` .</span><span class="sxs-lookup"><span data-stu-id="57b40-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="57b40-149">`Post`gli oggetti che potrebbero finire nello stato imprevisto vengono rimossi ed entrambe le query restituiscono 3 risultati.</span><span class="sxs-lookup"><span data-stu-id="57b40-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="57b40-150">Disabilitazione dei filtri</span><span class="sxs-lookup"><span data-stu-id="57b40-150">Disabling Filters</span></span>

<span data-ttu-id="57b40-151">È possibile disabilitare i filtri per singole query LINQ usando l'operatore `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="57b40-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="57b40-152">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="57b40-152">Limitations</span></span>

<span data-ttu-id="57b40-153">I filtri di query globali presentano le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="57b40-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="57b40-154">I filtri possono essere definiti solo per il tipo di entità radice di una gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="57b40-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
