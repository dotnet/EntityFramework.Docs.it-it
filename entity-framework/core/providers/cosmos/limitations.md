---
title: Provider di Azure Cosmos DB-limitazioni-EF Core
description: Limitazioni del provider di Azure Cosmos DB Entity Framework Core rispetto ad altri provider
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635718"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="ab631-103">Limitazioni del provider di Azure Cosmos DB EF Core</span><span class="sxs-lookup"><span data-stu-id="ab631-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="ab631-104">Il provider Cosmos presenta alcune limitazioni.</span><span class="sxs-lookup"><span data-stu-id="ab631-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="ab631-105">Molte di queste limitazioni sono dovute a limitazioni nel motore di database di Cosmos sottostante e non sono specifiche di EF.</span><span class="sxs-lookup"><span data-stu-id="ab631-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="ab631-106">Ma la cosa più semplice [non è stata ancora implementata](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="ab631-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="ab631-107">Di seguito sono riportate alcune delle funzionalità comunemente richieste:</span><span class="sxs-lookup"><span data-stu-id="ab631-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="ab631-108">`Include` supporto</span><span class="sxs-lookup"><span data-stu-id="ab631-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="ab631-109">`Join` supporto</span><span class="sxs-lookup"><span data-stu-id="ab631-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="ab631-110">Raccolte di tipi primitivi supportati</span><span class="sxs-lookup"><span data-stu-id="ab631-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="ab631-111">Limitazioni di Azure Cosmos DB SDK</span><span class="sxs-lookup"><span data-stu-id="ab631-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="ab631-112">Sono disponibili solo metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="ab631-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="ab631-113">Poiché non sono presenti versioni di sincronizzazione dei metodi di basso livello EF Core si basa su, la funzionalità corrispondente è attualmente implementata chiamando `.Wait()` sull'oggetto restituito `Task` .</span><span class="sxs-lookup"><span data-stu-id="ab631-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="ab631-114">Ciò significa che l'uso di metodi come `SaveChanges` o `ToList` anziché le relative controparti asincrone potrebbe causare un deadlock nell'applicazione</span><span class="sxs-lookup"><span data-stu-id="ab631-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="ab631-115">Limitazioni Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="ab631-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="ab631-116">È possibile visualizzare la panoramica completa delle [funzionalità supportate da Azure Cosmos DB](/azure/cosmos-db/modeling-data), che rappresentano le differenze più importanti rispetto a un database relazionale:</span><span class="sxs-lookup"><span data-stu-id="ab631-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="ab631-117">Le transazioni avviate dal client non sono supportate</span><span class="sxs-lookup"><span data-stu-id="ab631-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="ab631-118">Alcune query tra partizioni sono più lente a seconda degli operatori interessati (ad esempio `Skip/Take` o `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="ab631-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
