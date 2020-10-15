---
title: Metodo Load-EF6
description: Metodo Load in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/load-method
ms.openlocfilehash: 055d7dd6957a31cb876904af55a1126bb944d338
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065894"
---
# <a name="the-load-method"></a><span data-ttu-id="5cf53-103">Metodo Load</span><span class="sxs-lookup"><span data-stu-id="5cf53-103">The Load Method</span></span>
<span data-ttu-id="5cf53-104">Esistono diversi scenari in cui è possibile che si desideri caricare entità dal database nel contesto senza eseguire immediatamente operazioni con tali entità.</span><span class="sxs-lookup"><span data-stu-id="5cf53-104">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="5cf53-105">Un esempio valido è il caricamento di entità per data binding come descritto in [dati locali](xref:ef6/querying/local-data).</span><span class="sxs-lookup"><span data-stu-id="5cf53-105">A good example of this is loading entities for data binding as described in [Local Data](xref:ef6/querying/local-data).</span></span> <span data-ttu-id="5cf53-106">Un modo comune per eseguire questa operazione è scrivere una query LINQ e quindi chiamare ToList, solo per eliminare immediatamente l'elenco creato.</span><span class="sxs-lookup"><span data-stu-id="5cf53-106">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="5cf53-107">Il metodo di estensione Load funziona esattamente come ToList, ad eccezione del fatto che evita di creare completamente l'elenco.</span><span class="sxs-lookup"><span data-stu-id="5cf53-107">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="5cf53-108">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="5cf53-108">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="5cf53-109">Di seguito sono riportati due esempi di utilizzo di Load.</span><span class="sxs-lookup"><span data-stu-id="5cf53-109">Here are two examples of using Load.</span></span> <span data-ttu-id="5cf53-110">Il primo viene tratto da un Windows Forms data binding applicazione in cui viene utilizzato Load per eseguire una query per le entità prima dell'associazione alla raccolta locale, come descritto in [dati locali](xref:ef6/querying/local-data):</span><span class="sxs-lookup"><span data-stu-id="5cf53-110">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](xref:ef6/querying/local-data):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="5cf53-111">Nel secondo esempio viene illustrato l'utilizzo di Load per caricare una raccolta filtrata di entità correlate, come descritto in [caricamento di entità correlate](xref:ef6/querying/related-data):</span><span class="sxs-lookup"><span data-stu-id="5cf53-111">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](xref:ef6/querying/related-data):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
