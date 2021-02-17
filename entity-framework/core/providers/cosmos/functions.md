---
title: Mapping di funzioni-provider Azure Cosmos DB-EF Core
description: Mapping di funzioni del provider di EF Core Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543588"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="1b0fe-103">Mapping di funzioni del provider di EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="1b0fe-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="1b0fe-104">Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="1b0fe-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="1b0fe-105">.NET</span><span class="sxs-lookup"><span data-stu-id="1b0fe-105">.NET</span></span>                          | <span data-ttu-id="1b0fe-106">SQL</span><span class="sxs-lookup"><span data-stu-id="1b0fe-106">SQL</span></span>                              | <span data-ttu-id="1b0fe-107">Aggiunta in</span><span class="sxs-lookup"><span data-stu-id="1b0fe-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="1b0fe-108">raccolta. Contains (item)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-108">collection.Contains(item)</span></span>     | <span data-ttu-id="1b0fe-109">@item IN @collection</span><span class="sxs-lookup"><span data-stu-id="1b0fe-109">@item IN @collection</span></span>
<span data-ttu-id="1b0fe-110">EF. Functions. Random ()</span><span class="sxs-lookup"><span data-stu-id="1b0fe-110">EF.Functions.Random()</span></span>         | <span data-ttu-id="1b0fe-111">RAND ()</span><span class="sxs-lookup"><span data-stu-id="1b0fe-111">RAND()</span></span>                           | <span data-ttu-id="1b0fe-112">EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-112">EF Core 6.0</span></span>
<span data-ttu-id="1b0fe-113">stringValue. Contains (valore)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-113">stringValue.Contains(value)</span></span>   | <span data-ttu-id="1b0fe-114">CONTAINs ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1b0fe-114">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="1b0fe-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-115">EF Core 5.0</span></span>
<span data-ttu-id="1b0fe-116">stringValue. EndsWith (valore)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-116">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="1b0fe-117">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1b0fe-117">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="1b0fe-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-118">EF Core 5.0</span></span>
<span data-ttu-id="1b0fe-119">stringValue. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="1b0fe-119">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="1b0fe-120">LEFT ( @stringValue ,1)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-120">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="1b0fe-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-121">EF Core 5.0</span></span>
<span data-ttu-id="1b0fe-122">stringValue. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="1b0fe-122">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="1b0fe-123">RIGHT ( @stringValue ,1)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-123">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="1b0fe-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-124">EF Core 5.0</span></span>
<span data-ttu-id="1b0fe-125">stringValue. StartsWith (valore)</span><span class="sxs-lookup"><span data-stu-id="1b0fe-125">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="1b0fe-126">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1b0fe-126">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="1b0fe-127">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1b0fe-127">EF Core 5.0</span></span>
