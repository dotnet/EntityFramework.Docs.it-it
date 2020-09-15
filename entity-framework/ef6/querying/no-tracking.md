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
# <a name="no-tracking-queries"></a>senza rilevamento delle modifiche
In alcuni casi può essere necessario recuperare le entità da una query, ma queste entità non vengono rilevate dal contesto. Ciò può comportare prestazioni migliori quando si eseguono query su un numero elevato di entità in scenari di sola lettura. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

Un nuovo metodo di estensione AsNoTracking consente l'esecuzione di qualsiasi query in questo modo. Ad esempio:  

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
