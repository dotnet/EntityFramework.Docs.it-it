---
title: Tag delle query - EF Core
description: Uso dei tag di query per identificare query specifiche nei messaggi di log emessi da Entity Framework Core
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065732"
---
# <a name="query-tags"></a><span data-ttu-id="44e26-103">Tag delle query</span><span class="sxs-lookup"><span data-stu-id="44e26-103">Query tags</span></span>

<span data-ttu-id="44e26-104">I tag di query consentono di correlare le query LINQ nel codice con query SQL generate acquisite nei log.</span><span class="sxs-lookup"><span data-stu-id="44e26-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="44e26-105">È possibile annotare una query LINQ usando il nuovo metodo `TagWith()`:</span><span class="sxs-lookup"><span data-stu-id="44e26-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="44e26-106">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="44e26-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="44e26-107">Questa query LINQ viene convertita nell'istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="44e26-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="44e26-108">È possibile chiamare `TagWith()` più volte sulla stessa query.</span><span class="sxs-lookup"><span data-stu-id="44e26-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="44e26-109">I tag delle query sono cumulativi.</span><span class="sxs-lookup"><span data-stu-id="44e26-109">Query tags are cumulative.</span></span>
<span data-ttu-id="44e26-110">Considerati i metodi seguenti, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="44e26-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="44e26-111">La query seguente:</span><span class="sxs-lookup"><span data-stu-id="44e26-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="44e26-112">Viene convertita in:</span><span class="sxs-lookup"><span data-stu-id="44e26-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="44e26-113">È anche possibile usare stringhe con più righe come tag di query.</span><span class="sxs-lookup"><span data-stu-id="44e26-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="44e26-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="44e26-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="44e26-115">Produce il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="44e26-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="44e26-116">Limitazioni note</span><span class="sxs-lookup"><span data-stu-id="44e26-116">Known limitations</span></span>

<span data-ttu-id="44e26-117">**I tag delle query non sono parametrizzabili:** EF Core considera sempre i tag delle query nella query LINQ come valori letterali stringa inclusi nel codice SQL generato.</span><span class="sxs-lookup"><span data-stu-id="44e26-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="44e26-118">Non sono consentite query compilate che accettano i tag di query come parametri.</span><span class="sxs-lookup"><span data-stu-id="44e26-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
