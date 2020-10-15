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
# <a name="the-load-method"></a>Metodo Load
Esistono diversi scenari in cui è possibile che si desideri caricare entità dal database nel contesto senza eseguire immediatamente operazioni con tali entità. Un esempio valido è il caricamento di entità per data binding come descritto in [dati locali](xref:ef6/querying/local-data). Un modo comune per eseguire questa operazione è scrivere una query LINQ e quindi chiamare ToList, solo per eliminare immediatamente l'elenco creato. Il metodo di estensione Load funziona esattamente come ToList, ad eccezione del fatto che evita di creare completamente l'elenco.  

Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

Di seguito sono riportati due esempi di utilizzo di Load. Il primo viene tratto da un Windows Forms data binding applicazione in cui viene utilizzato Load per eseguire una query per le entità prima dell'associazione alla raccolta locale, come descritto in [dati locali](xref:ef6/querying/local-data):  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

Nel secondo esempio viene illustrato l'utilizzo di Load per caricare una raccolta filtrata di entità correlate, come descritto in [caricamento di entità correlate](xref:ef6/querying/related-data):  

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
