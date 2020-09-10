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
# <a name="automatic-detect-changes"></a><span data-ttu-id="0105d-103">Rilevamento automatico delle modifiche</span><span class="sxs-lookup"><span data-stu-id="0105d-103">Automatic detect changes</span></span>
<span data-ttu-id="0105d-104">Quando si utilizza la maggior parte delle entità POCO, la determinazione del modo in cui un'entità è cambiata (e di conseguenza gli aggiornamenti che devono essere inviati al database) viene gestita dall'algoritmo di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="0105d-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="0105d-105">Il rilevamento delle modifiche funziona rilevando le differenze tra i valori di proprietà correnti dell'entità e i valori di proprietà originali archiviati in uno snapshot quando l'entità è stata sottoposta a query o collegata.</span><span class="sxs-lookup"><span data-stu-id="0105d-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="0105d-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="0105d-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="0105d-107">Per impostazione predefinita, Entity Framework esegue il rilevamento automatico delle modifiche quando vengono chiamati i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0105d-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="0105d-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="0105d-108">DbSet.Find</span></span>  
- <span data-ttu-id="0105d-109">DbSet. local</span><span class="sxs-lookup"><span data-stu-id="0105d-109">DbSet.Local</span></span>  
- <span data-ttu-id="0105d-110">DbSet. Add</span><span class="sxs-lookup"><span data-stu-id="0105d-110">DbSet.Add</span></span>  
- <span data-ttu-id="0105d-111">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="0105d-111">DbSet.AddRange</span></span>
- <span data-ttu-id="0105d-112">DbSet. Remove</span><span class="sxs-lookup"><span data-stu-id="0105d-112">DbSet.Remove</span></span>  
- <span data-ttu-id="0105d-113">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="0105d-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="0105d-114">DbSet. Connetti</span><span class="sxs-lookup"><span data-stu-id="0105d-114">DbSet.Attach</span></span>  
- <span data-ttu-id="0105d-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="0105d-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="0105d-116">DbContext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="0105d-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="0105d-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="0105d-117">DbContext.Entry</span></span>  
- <span data-ttu-id="0105d-118">DbChangeTracker. entrys</span><span class="sxs-lookup"><span data-stu-id="0105d-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="0105d-119">Disabilitazione del rilevamento automatico delle modifiche</span><span class="sxs-lookup"><span data-stu-id="0105d-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="0105d-120">Se si tiene traccia di molte entità nel contesto e si chiama uno di questi metodi più volte in un ciclo, è possibile ottenere miglioramenti significativi delle prestazioni disattivando il rilevamento delle modifiche per la durata del ciclo.</span><span class="sxs-lookup"><span data-stu-id="0105d-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="0105d-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0105d-121">For example:</span></span>  

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

<span data-ttu-id="0105d-122">Non dimenticare di riabilitare il rilevamento delle modifiche dopo il ciclo: è stato usato un try/finally per assicurarsi che venga sempre riabilitata anche se il codice nel ciclo genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="0105d-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="0105d-123">Un'alternativa alla disabilitazione e riabilitazione consiste nel lasciare il rilevamento automatico delle modifiche disattivate in qualsiasi momento e il contesto di chiamata. ChangeTracker. DetectChanges in modo esplicito o utilizzare i proxy di rilevamento delle modifiche diligentemente.</span><span class="sxs-lookup"><span data-stu-id="0105d-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="0105d-124">Entrambe le opzioni sono avanzate e possono introdurre facilmente bug sottili nell'applicazione, in modo da usarli con cautela.</span><span class="sxs-lookup"><span data-stu-id="0105d-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="0105d-125">Se è necessario aggiungere o rimuovere molti oggetti da un contesto, provare a usare DbSet. AddRange e DbSet. RemoveRange.</span><span class="sxs-lookup"><span data-stu-id="0105d-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="0105d-126">Questo metodo rileva automaticamente le modifiche solo una volta dopo il completamento delle operazioni di aggiunta o rimozione.</span><span class="sxs-lookup"><span data-stu-id="0105d-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
