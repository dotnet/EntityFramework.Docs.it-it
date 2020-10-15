---
title: Mapping di funzioni-provider Azure Cosmos DB-EF Core
description: Mapping di funzioni del provider di EF Core Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066608"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="13fe7-103">Mapping di funzioni del provider di EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="13fe7-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="13fe7-104">Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="13fe7-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="13fe7-105">.NET</span><span class="sxs-lookup"><span data-stu-id="13fe7-105">.NET</span></span>                          | <span data-ttu-id="13fe7-106">SQL</span><span class="sxs-lookup"><span data-stu-id="13fe7-106">SQL</span></span>                              | <span data-ttu-id="13fe7-107">Aggiunta in</span><span class="sxs-lookup"><span data-stu-id="13fe7-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="13fe7-108">raccolta. Contains (item)</span><span class="sxs-lookup"><span data-stu-id="13fe7-108">collection.Contains(item)</span></span>     | <span data-ttu-id="13fe7-109">@item IN @collection</span><span class="sxs-lookup"><span data-stu-id="13fe7-109">@item IN @collection</span></span>
<span data-ttu-id="13fe7-110">stringValue. Contains (valore)</span><span class="sxs-lookup"><span data-stu-id="13fe7-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="13fe7-111">CONTAINs ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="13fe7-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="13fe7-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="13fe7-112">EF Core 5.0</span></span>
<span data-ttu-id="13fe7-113">stringValue. EndsWith (valore)</span><span class="sxs-lookup"><span data-stu-id="13fe7-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="13fe7-114">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="13fe7-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="13fe7-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="13fe7-115">EF Core 5.0</span></span>
<span data-ttu-id="13fe7-116">stringValue. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="13fe7-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="13fe7-117">LEFT ( @stringValue ,1)</span><span class="sxs-lookup"><span data-stu-id="13fe7-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="13fe7-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="13fe7-118">EF Core 5.0</span></span>
<span data-ttu-id="13fe7-119">stringValue. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="13fe7-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="13fe7-120">RIGHT ( @stringValue ,1)</span><span class="sxs-lookup"><span data-stu-id="13fe7-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="13fe7-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="13fe7-121">EF Core 5.0</span></span>
<span data-ttu-id="13fe7-122">stringValue. StartsWith (valore)</span><span class="sxs-lookup"><span data-stu-id="13fe7-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="13fe7-123">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="13fe7-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="13fe7-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="13fe7-124">EF Core 5.0</span></span>
