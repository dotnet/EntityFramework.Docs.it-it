---
title: Provider di Azure Cosmos DB-limitazioni-EF Core
description: Limitazioni del provider di Azure Cosmos DB Entity Framework Core rispetto ad altri provider
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: e4d1c38da14d1e722797f8543313a69c7fb088cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064037"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="11e84-103">Limitazioni del provider di Azure Cosmos DB EF Core</span><span class="sxs-lookup"><span data-stu-id="11e84-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="11e84-104">Il provider Cosmos presenta alcune limitazioni.</span><span class="sxs-lookup"><span data-stu-id="11e84-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="11e84-105">Molte di queste limitazioni sono dovute a limitazioni nel motore di database di Cosmos sottostante e non sono specifiche di EF.</span><span class="sxs-lookup"><span data-stu-id="11e84-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="11e84-106">Ma la cosa più semplice [non è stata ancora implementata](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="11e84-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="11e84-107">Limitazioni temporanee</span><span class="sxs-lookup"><span data-stu-id="11e84-107">Temporary limitations</span></span>

- <span data-ttu-id="11e84-108">Anche se è presente un solo tipo di entità senza ereditarietà mappata a un contenitore, è ancora presente una proprietà Discriminator.</span><span class="sxs-lookup"><span data-stu-id="11e84-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="11e84-109">I tipi di entità con chiavi di partizione non funzionano correttamente in alcuni scenari</span><span class="sxs-lookup"><span data-stu-id="11e84-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="11e84-110">`Include` le chiamate non sono supportate</span><span class="sxs-lookup"><span data-stu-id="11e84-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="11e84-111">`Join` le chiamate non sono supportate</span><span class="sxs-lookup"><span data-stu-id="11e84-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="11e84-112">Limitazioni di Azure Cosmos DB SDK</span><span class="sxs-lookup"><span data-stu-id="11e84-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="11e84-113">Sono disponibili solo metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="11e84-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="11e84-114">Poiché non sono presenti versioni di sincronizzazione dei metodi di basso livello EF Core si basa su, la funzionalità corrispondente è attualmente implementata chiamando `.Wait()` sull'oggetto restituito `Task` .</span><span class="sxs-lookup"><span data-stu-id="11e84-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="11e84-115">Ciò significa che l'uso di metodi come `SaveChanges` o `ToList` anziché le relative controparti asincrone potrebbe causare un deadlock nell'applicazione</span><span class="sxs-lookup"><span data-stu-id="11e84-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="11e84-116">Limitazioni Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="11e84-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="11e84-117">È possibile visualizzare la panoramica completa delle [funzionalità supportate da Azure Cosmos DB](/azure/cosmos-db/modeling-data), che rappresentano le differenze più importanti rispetto a un database relazionale:</span><span class="sxs-lookup"><span data-stu-id="11e84-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="11e84-118">Le transazioni avviate dal client non sono supportate</span><span class="sxs-lookup"><span data-stu-id="11e84-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="11e84-119">Alcune query tra partizioni non sono supportate o molto più lente a seconda degli operatori interessati</span><span class="sxs-lookup"><span data-stu-id="11e84-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
