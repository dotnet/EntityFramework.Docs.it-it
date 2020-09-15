---
title: Caricamento esplicito dei dati correlati-EF Core
description: Caricamento esplicito di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078953"
---
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="cfa52-103">Caricamento esplicito di dati correlati</span><span class="sxs-lookup"><span data-stu-id="cfa52-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="cfa52-104">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="cfa52-104">Explicit loading</span></span>

<span data-ttu-id="cfa52-105">È possibile caricare in modo esplicito una proprietà di navigazione tramite l'API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="cfa52-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="cfa52-106">È anche possibile caricare in modo esplicito una proprietà di navigazione eseguendo una query separata che restituisce le entità correlate.</span><span class="sxs-lookup"><span data-stu-id="cfa52-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="cfa52-107">Se il rilevamento delle modifiche è abilitato, quando la query materializza un'entità, EF Core imposterà automaticamente le proprietà di navigazione dell'entità appena caricata in modo da fare riferimento alle entità già caricate e impostare le proprietà di navigazione delle entità già caricate in modo che facciano riferimento all'entità appena caricata.</span><span class="sxs-lookup"><span data-stu-id="cfa52-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="cfa52-108">Esecuzione di query su entità correlate</span><span class="sxs-lookup"><span data-stu-id="cfa52-108">Querying related entities</span></span>

<span data-ttu-id="cfa52-109">È anche possibile ottenere una query LINQ che rappresenta il contenuto di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="cfa52-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="cfa52-110">Consente di applicare operatori aggiuntivi alla query.</span><span class="sxs-lookup"><span data-stu-id="cfa52-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="cfa52-111">Esempio di nemico che applica un operatore di aggregazione sulle entità correlate senza caricarli in memoria.</span><span class="sxs-lookup"><span data-stu-id="cfa52-111">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="cfa52-112">È anche possibile filtrare le entità correlate che vengono caricate in memoria.</span><span class="sxs-lookup"><span data-stu-id="cfa52-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
