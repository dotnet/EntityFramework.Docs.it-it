---
title: Query senza rilevamento-EF6
description: Query senza rilevamento in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620287"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="27e63-103">senza rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="27e63-103">No-Tracking Queries</span></span>
<span data-ttu-id="27e63-104">In alcuni casi può essere necessario recuperare le entità da una query, ma queste entità non vengono rilevate dal contesto.</span><span class="sxs-lookup"><span data-stu-id="27e63-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="27e63-105">Ciò può comportare prestazioni migliori quando si eseguono query su un numero elevato di entità in scenari di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="27e63-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="27e63-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="27e63-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="27e63-107">Un nuovo metodo di estensione AsNoTracking consente l'esecuzione di qualsiasi query in questo modo.</span><span class="sxs-lookup"><span data-stu-id="27e63-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="27e63-108">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="27e63-108">For example:</span></span>  

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
