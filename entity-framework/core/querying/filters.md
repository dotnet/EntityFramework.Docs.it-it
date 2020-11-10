---
title: Filtri di query globali - EF Core
description: Uso di filtri di query globali per filtrare i risultati con Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 6436f9f8e2e09d44ef9528fd2022720d40095fe0
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430131"
---
# <a name="global-query-filters"></a><span data-ttu-id="de062-103">Filtri di query globali</span><span class="sxs-lookup"><span data-stu-id="de062-103">Global Query Filters</span></span>

<span data-ttu-id="de062-104">I filtri di query globali sono predicati di query LINQ applicati ai tipi di entità nel modello di metadati (in genere in `OnModelCreating` ).</span><span class="sxs-lookup"><span data-stu-id="de062-104">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in `OnModelCreating`).</span></span> <span data-ttu-id="de062-105">Un predicato di query è un'espressione booleana che in genere viene passata all' `Where` operatore di query LINQ.</span><span class="sxs-lookup"><span data-stu-id="de062-105">A query predicate is a boolean expression typically passed to the LINQ `Where` query operator.</span></span>  <span data-ttu-id="de062-106">EF Core applica automaticamente tali filtri alle query LINQ che coinvolgono tali tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="de062-106">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="de062-107">EF Core li applica anche ai tipi di entità, a cui viene fatto riferimento indirettamente tramite l'utilizzo della proprietà di navigazione o di inclusione.</span><span class="sxs-lookup"><span data-stu-id="de062-107">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="de062-108">Alcune applicazioni comuni di questa funzionalità sono:</span><span class="sxs-lookup"><span data-stu-id="de062-108">Some common applications of this feature are:</span></span>

* <span data-ttu-id="de062-109">**Soft delete** : un tipo di entità definisce una `IsDeleted` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="de062-109">**Soft delete** - An Entity Type defines an `IsDeleted` property.</span></span>
* <span data-ttu-id="de062-110">**Multi-tenant** : un tipo di entità definisce una `TenantId` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="de062-110">**Multi-tenancy** - An Entity Type defines a `TenantId` property.</span></span>

## <a name="example"></a><span data-ttu-id="de062-111">Esempio</span><span class="sxs-lookup"><span data-stu-id="de062-111">Example</span></span>

<span data-ttu-id="de062-112">Nell'esempio seguente viene illustrato come utilizzare i filtri di query globali per implementare comportamenti di query di multi-tenant e di eliminazione temporanea in un semplice modello di Blog.</span><span class="sxs-lookup"><span data-stu-id="de062-112">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="de062-113">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="de062-113">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="de062-114">Prima di tutto, definire le entità:</span><span class="sxs-lookup"><span data-stu-id="de062-114">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

<span data-ttu-id="de062-115">Si noti la dichiarazione di un `_tenantId` campo nell' `Blog` entità.</span><span class="sxs-lookup"><span data-stu-id="de062-115">Note the declaration of a `_tenantId` field on the `Blog` entity.</span></span> <span data-ttu-id="de062-116">Questo campo verrà usato per associare ogni istanza di Blog a un tenant specifico.</span><span class="sxs-lookup"><span data-stu-id="de062-116">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="de062-117">Definito anche come `IsDeleted` proprietà nel tipo di `Post` entità.</span><span class="sxs-lookup"><span data-stu-id="de062-117">Also defined is an `IsDeleted` property on the `Post` entity type.</span></span> <span data-ttu-id="de062-118">Questa proprietà viene usata per tenere traccia del fatto che un'istanza post è stata "eliminata temporaneamente".</span><span class="sxs-lookup"><span data-stu-id="de062-118">This property is used to keep track of whether a post instance has been "soft-deleted".</span></span> <span data-ttu-id="de062-119">L'istanza è contrassegnata come eliminata senza rimuovere fisicamente i dati sottostanti.</span><span class="sxs-lookup"><span data-stu-id="de062-119">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="de062-120">Configurare quindi i filtri query in `OnModelCreating` usando l' `HasQueryFilter` API.</span><span class="sxs-lookup"><span data-stu-id="de062-120">Next, configure the query filters in `OnModelCreating` using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

<span data-ttu-id="de062-121">Le espressioni del predicato passate alle `HasQueryFilter` chiamate verranno ora applicate automaticamente a qualsiasi query LINQ per tali tipi.</span><span class="sxs-lookup"><span data-stu-id="de062-121">The predicate expressions passed to the `HasQueryFilter` calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="de062-122">Si noti l'uso del campo a livello di istanza di DbContext `_tenantId`, usato per impostare il tenant corrente.</span><span class="sxs-lookup"><span data-stu-id="de062-122">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="de062-123">I filtri a livello di modello useranno il valore dell'istanza del contesto corretta, ovvero l'istanza che esegue la query.</span><span class="sxs-lookup"><span data-stu-id="de062-123">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="de062-124">Attualmente non è possibile definire più filtri query sulla stessa entità. verrà applicato solo l'ultimo.</span><span class="sxs-lookup"><span data-stu-id="de062-124">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="de062-125">Tuttavia, è possibile definire un singolo filtro con più condizioni usando l' `AND` operatore logico ([ `&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="de062-125">However, you can define a single filter with multiple conditions using the logical `AND` operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="de062-126">Uso delle navigazioni</span><span class="sxs-lookup"><span data-stu-id="de062-126">Use of navigations</span></span>

<span data-ttu-id="de062-127">È inoltre possibile utilizzare le navigazioni nella definizione dei filtri di query globali.</span><span class="sxs-lookup"><span data-stu-id="de062-127">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="de062-128">Se si utilizzano le navigazioni in filtro query, i filtri query verranno applicati in modo ricorsivo.</span><span class="sxs-lookup"><span data-stu-id="de062-128">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="de062-129">Quando EF Core espande le navigazioni utilizzate nei filtri di query, verranno applicati anche i filtri di query definiti sulle entità a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="de062-129">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

<span data-ttu-id="de062-130">Per illustrare questa configurazione, configurare i filtri query in `OnModelCreating` nel modo seguente: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]</span><span class="sxs-lookup"><span data-stu-id="de062-130">To illustrate this configure query filters in `OnModelCreating` in the following way: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]</span></span>

<span data-ttu-id="de062-131">Eseguire quindi una query per tutte le `Blog` entità: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]</span><span class="sxs-lookup"><span data-stu-id="de062-131">Next, query for all `Blog` entities: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]</span></span>

<span data-ttu-id="de062-132">Questa query produce il codice SQL seguente che applica i filtri di query definiti per le `Blog` `Post` entità e:</span><span class="sxs-lookup"><span data-stu-id="de062-132">This query produces the following SQL, which applies query filters defined for both `Blog` and `Post` entities:</span></span>

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> <span data-ttu-id="de062-133">Attualmente EF Core non rileva i cicli nelle definizioni di filtro delle query globali, quindi è necessario prestare attenzione durante la definizione.</span><span class="sxs-lookup"><span data-stu-id="de062-133">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="de062-134">Se specificato in modo non corretto, i cicli potrebbero causare cicli infiniti durante la conversione delle query.</span><span class="sxs-lookup"><span data-stu-id="de062-134">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="de062-135">Accesso all'entità con filtro query tramite la navigazione obbligatoria</span><span class="sxs-lookup"><span data-stu-id="de062-135">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="de062-136">L'uso della navigazione necessaria per accedere a un'entità con un filtro di query globale definito può causare risultati imprevisti.</span><span class="sxs-lookup"><span data-stu-id="de062-136">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="de062-137">Per la navigazione obbligatoria si prevede che l'entità correlata sia sempre presente.</span><span class="sxs-lookup"><span data-stu-id="de062-137">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="de062-138">Se l'entità correlata necessaria viene esclusa dal filtro di query, l'entità padre non sarà nel risultato.</span><span class="sxs-lookup"><span data-stu-id="de062-138">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="de062-139">Quindi, è possibile ottenere meno elementi del previsto.</span><span class="sxs-lookup"><span data-stu-id="de062-139">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="de062-140">Per illustrare il problema, è possibile usare le `Blog` `Post` entità e specificate in precedenza e il `OnModelCreating` metodo seguente:</span><span class="sxs-lookup"><span data-stu-id="de062-140">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following `OnModelCreating` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

<span data-ttu-id="de062-141">È possibile effettuare il seeding del modello con i dati seguenti:</span><span class="sxs-lookup"><span data-stu-id="de062-141">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

<span data-ttu-id="de062-142">Il problema può essere rilevato durante l'esecuzione di due query:</span><span class="sxs-lookup"><span data-stu-id="de062-142">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

<span data-ttu-id="de062-143">Con l'installazione precedente, la prima query restituisce tutti i 6 `Post` , tuttavia la seconda query restituisce solo 3.</span><span class="sxs-lookup"><span data-stu-id="de062-143">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="de062-144">Questa mancata corrispondenza si verifica perché `Include` il metodo nella seconda query carica le `Blog` entità correlate.</span><span class="sxs-lookup"><span data-stu-id="de062-144">This mismatch happens because `Include` method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="de062-145">Poiché la navigazione tra `Blog` e `Post` è obbligatoria, EF core utilizza `INNER JOIN` quando si costruisce la query:</span><span class="sxs-lookup"><span data-stu-id="de062-145">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="de062-146">L'uso di `INNER JOIN` Filtra tutti i i `Post` cui oggetti correlati `Blog` sono stati rimossi da un filtro di query globale.</span><span class="sxs-lookup"><span data-stu-id="de062-146">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="de062-147">Può essere risolto usando la navigazione facoltativa anziché obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="de062-147">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="de062-148">In questo modo la prima query rimane invariata, ma la seconda query genera ora `LEFT JOIN` e restituisce 6 risultati.</span><span class="sxs-lookup"><span data-stu-id="de062-148">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

<span data-ttu-id="de062-149">Un approccio alternativo consiste nello specificare filtri coerenti in entrambe `Blog` le `Post` entità e.</span><span class="sxs-lookup"><span data-stu-id="de062-149">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="de062-150">In questo modo, i filtri corrispondenti vengono applicati sia a `Blog` che a `Post` .</span><span class="sxs-lookup"><span data-stu-id="de062-150">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="de062-151">`Post`gli oggetti che potrebbero finire nello stato imprevisto vengono rimossi ed entrambe le query restituiscono 3 risultati.</span><span class="sxs-lookup"><span data-stu-id="de062-151">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a><span data-ttu-id="de062-152">Disabilitazione dei filtri</span><span class="sxs-lookup"><span data-stu-id="de062-152">Disabling Filters</span></span>

<span data-ttu-id="de062-153">È possibile disabilitare i filtri per singole query LINQ usando l'operatore <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>.</span><span class="sxs-lookup"><span data-stu-id="de062-153">Filters may be disabled for individual LINQ queries by using the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> operator.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="de062-154">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="de062-154">Limitations</span></span>

<span data-ttu-id="de062-155">I filtri di query globali presentano le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="de062-155">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="de062-156">I filtri possono essere definiti solo per il tipo di entità radice di una gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="de062-156">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
