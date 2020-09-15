---
title: Query senza rilevamento-EF6
description: Query senza rilevamento in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073899"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="c20dd-103">senza rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="c20dd-103">No-Tracking Queries</span></span>
<span data-ttu-id="c20dd-104">In alcuni casi può essere necessario recuperare le entità da una query, ma queste entità non vengono rilevate dal contesto.</span><span class="sxs-lookup"><span data-stu-id="c20dd-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="c20dd-105">Ciò può comportare prestazioni migliori quando si eseguono query su un numero elevato di entità in scenari di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="c20dd-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="c20dd-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="c20dd-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c20dd-107">Un nuovo metodo di estensione AsNoTracking consente l'esecuzione di qualsiasi query in questo modo.</span><span class="sxs-lookup"><span data-stu-id="c20dd-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="c20dd-108">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c20dd-108">For example:</span></span>  

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
