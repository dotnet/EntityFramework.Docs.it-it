---
title: Rilevamento automatico delle modifiche-EF6
description: Rilevamento automatico delle modifiche in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 938af809ac89d3490da9885497fc5601453e1c34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619999"
---
# <a name="automatic-detect-changes"></a>Rilevamento automatico delle modifiche
Quando si utilizza la maggior parte delle entità POCO, la determinazione del modo in cui un'entità è cambiata (e di conseguenza gli aggiornamenti che devono essere inviati al database) viene gestita dall'algoritmo di rilevamento delle modifiche. Il rilevamento delle modifiche funziona rilevando le differenze tra i valori di proprietà correnti dell'entità e i valori di proprietà originali archiviati in uno snapshot quando l'entità è stata sottoposta a query o collegata. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

Per impostazione predefinita, Entity Framework esegue il rilevamento automatico delle modifiche quando vengono chiamati i metodi seguenti:  

- DbSet.Find  
- DbSet. local  
- DbSet. Add  
- DbSet. AddRange
- DbSet. Remove  
- DbSet. RemoveRange
- DbSet. Connetti  
- DbContext.SaveChanges  
- DbContext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker. entrys  

## <a name="disabling-automatic-detection-of-changes"></a>Disabilitazione del rilevamento automatico delle modifiche  

Se si tiene traccia di molte entità nel contesto e si chiama uno di questi metodi più volte in un ciclo, è possibile ottenere miglioramenti significativi delle prestazioni disattivando il rilevamento delle modifiche per la durata del ciclo. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

Non dimenticare di riabilitare il rilevamento delle modifiche dopo il ciclo: è stato usato un try/finally per assicurarsi che venga sempre riabilitata anche se il codice nel ciclo genera un'eccezione.  

Un'alternativa alla disabilitazione e riabilitazione consiste nel lasciare il rilevamento automatico delle modifiche disattivate in qualsiasi momento e il contesto di chiamata. ChangeTracker. DetectChanges in modo esplicito o utilizzare i proxy di rilevamento delle modifiche diligentemente. Entrambe le opzioni sono avanzate e possono introdurre facilmente bug sottili nell'applicazione, in modo da usarli con cautela.  

Se è necessario aggiungere o rimuovere molti oggetti da un contesto, provare a usare DbSet. AddRange e DbSet. RemoveRange. Questo metodo rileva automaticamente le modifiche solo una volta dopo il completamento delle operazioni di aggiunta o rimozione. 
