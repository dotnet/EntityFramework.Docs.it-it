---
title: Query No-Tracking-EF6
description: Query di No-Tracking in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: 74826d3052cf6a249796db2845fa6b96e5ecb8f9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065862"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="638f8-103">senza rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="638f8-103">No-Tracking Queries</span></span>
<span data-ttu-id="638f8-104">In alcuni casi può essere necessario recuperare le entità da una query, ma queste entità non vengono rilevate dal contesto.</span><span class="sxs-lookup"><span data-stu-id="638f8-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="638f8-105">Ciò può comportare prestazioni migliori quando si eseguono query su un numero elevato di entità in scenari di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="638f8-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="638f8-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="638f8-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="638f8-107">Un nuovo metodo di estensione AsNoTracking consente l'esecuzione di qualsiasi query in questo modo.</span><span class="sxs-lookup"><span data-stu-id="638f8-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="638f8-108">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="638f8-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
