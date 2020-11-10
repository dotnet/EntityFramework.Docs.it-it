---
title: Provider di Azure Cosmos DB-limitazioni-EF Core
description: Limitazioni del provider di Azure Cosmos DB Entity Framework Core rispetto ad altri provider
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430196"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="b218b-103">Limitazioni del provider di Azure Cosmos DB EF Core</span><span class="sxs-lookup"><span data-stu-id="b218b-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="b218b-104">Il provider Cosmos presenta alcune limitazioni.</span><span class="sxs-lookup"><span data-stu-id="b218b-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="b218b-105">Molte di queste limitazioni sono dovute a limitazioni nel motore di database di Cosmos sottostante e non sono specifiche di EF.</span><span class="sxs-lookup"><span data-stu-id="b218b-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="b218b-106">Ma la cosa più semplice [non è stata ancora implementata](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="b218b-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="b218b-107">Limitazioni temporanee</span><span class="sxs-lookup"><span data-stu-id="b218b-107">Temporary limitations</span></span>

- <span data-ttu-id="b218b-108">`Include` le chiamate non sono supportate</span><span class="sxs-lookup"><span data-stu-id="b218b-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="b218b-109">`Join` le chiamate non sono supportate</span><span class="sxs-lookup"><span data-stu-id="b218b-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="b218b-110">Limitazioni di Azure Cosmos DB SDK</span><span class="sxs-lookup"><span data-stu-id="b218b-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="b218b-111">Sono disponibili solo metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="b218b-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="b218b-112">Poiché non sono presenti versioni di sincronizzazione dei metodi di basso livello EF Core si basa su, la funzionalità corrispondente è attualmente implementata chiamando `.Wait()` sull'oggetto restituito `Task` .</span><span class="sxs-lookup"><span data-stu-id="b218b-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="b218b-113">Ciò significa che l'uso di metodi come `SaveChanges` o `ToList` anziché le relative controparti asincrone potrebbe causare un deadlock nell'applicazione</span><span class="sxs-lookup"><span data-stu-id="b218b-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="b218b-114">Limitazioni Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="b218b-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="b218b-115">È possibile visualizzare la panoramica completa delle [funzionalità supportate da Azure Cosmos DB](/azure/cosmos-db/modeling-data), che rappresentano le differenze più importanti rispetto a un database relazionale:</span><span class="sxs-lookup"><span data-stu-id="b218b-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="b218b-116">Le transazioni avviate dal client non sono supportate</span><span class="sxs-lookup"><span data-stu-id="b218b-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="b218b-117">Alcune query tra partizioni sono più lente a seconda degli operatori interessati (ad esempio `Skip/Take` o `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="b218b-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
