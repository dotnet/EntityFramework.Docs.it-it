---
title: Debug del rilevamento modifiche-EF Core
description: Uso di ChangeTracker DebugView e dei messaggi di log per eseguire il debug EF Core rilevamento modifiche
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: cda81728beb5ffbc8604c191e84b3553a7343ec0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023172"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="fefdb-103">Debug del rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="fefdb-103">Change Tracker Debugging</span></span>

<span data-ttu-id="fefdb-104">Lo strumento di rilevamento delle modifiche Entity Framework Core (EF Core) genera due tipi di output per semplificare il debug:</span><span class="sxs-lookup"><span data-stu-id="fefdb-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="fefdb-105"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>Fornisce una visualizzazione leggibile di tutte le entità rilevate</span><span class="sxs-lookup"><span data-stu-id="fefdb-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="fefdb-106">I messaggi di log a livello di debug vengono generati quando lo strumento di rilevamento modifiche rileva lo stato e corregge le relazioni</span><span class="sxs-lookup"><span data-stu-id="fefdb-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="fefdb-107">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="fefdb-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="fefdb-108">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="fefdb-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="fefdb-109">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).</span><span class="sxs-lookup"><span data-stu-id="fefdb-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="fefdb-110">Visualizzazione debug di rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="fefdb-110">Change tracker debug view</span></span>

<span data-ttu-id="fefdb-111">È possibile accedere alla visualizzazione di debug del rilevamento modifiche nel debugger dell'IDE.</span><span class="sxs-lookup"><span data-stu-id="fefdb-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="fefdb-112">Ad esempio, con Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fefdb-112">For example, with Visual Studio:</span></span>

![Accesso alla visualizzazione di debug di change tracker dal debugger di Visual Studio](_static/debug-view.png)

<span data-ttu-id="fefdb-114">È anche possibile accedervi direttamente dal codice, ad esempio per inviare la visualizzazione debug alla console:</span><span class="sxs-lookup"><span data-stu-id="fefdb-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="fefdb-115">La visualizzazione debug presenta una forma breve e un formato lungo.</span><span class="sxs-lookup"><span data-stu-id="fefdb-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="fefdb-116">La forma abbreviata Mostra le entità rilevate, il relativo stato e i valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="fefdb-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="fefdb-117">Il formato Long include anche tutti i valori di proprietà e di navigazione e lo stato.</span><span class="sxs-lookup"><span data-stu-id="fefdb-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="fefdb-118">Visualizzazione breve</span><span class="sxs-lookup"><span data-stu-id="fefdb-118">The short view</span></span>

<span data-ttu-id="fefdb-119">Verrà ora esaminato un esempio di visualizzazione di debug utilizzando il modello illustrato alla fine di questo documento.</span><span class="sxs-lookup"><span data-stu-id="fefdb-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="fefdb-120">Prima di tutto, si registreranno alcune entità e le si inseriranno in alcuni Stati diversi, in modo da visualizzare i dati di rilevamento delle modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="fefdb-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="fefdb-121">Stampando la visualizzazione breve a questo punto, come illustrato in precedenza, viene restituito l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="fefdb-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="fefdb-122">Notare:</span><span class="sxs-lookup"><span data-stu-id="fefdb-122">Notice:</span></span>

- <span data-ttu-id="fefdb-123">Ogni entità rilevata è elencata con il relativo valore di chiave primaria (PK).</span><span class="sxs-lookup"><span data-stu-id="fefdb-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="fefdb-124">Ad esempio: `Blog {Id: 1}`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="fefdb-125">Se l'entità è un tipo di entità di tipo condiviso, viene visualizzato anche il tipo CLR.</span><span class="sxs-lookup"><span data-stu-id="fefdb-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="fefdb-126">Ad esempio: `PostTag (Dictionary<string, object>)`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="fefdb-127">Il <xref:Microsoft.EntityFrameworkCore.EntityState> viene visualizzato avanti.</span><span class="sxs-lookup"><span data-stu-id="fefdb-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="fefdb-128">Si tratta di uno tra `Unchanged` , `Added` , `Modified` o `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="fefdb-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="fefdb-129">Vengono visualizzati i valori per le chiavi alternative (AKs).</span><span class="sxs-lookup"><span data-stu-id="fefdb-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="fefdb-130">Ad esempio: `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="fefdb-131">Infine, vengono visualizzati i valori per le chiavi esterne (FKs).</span><span class="sxs-lookup"><span data-stu-id="fefdb-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="fefdb-132">Ad esempio: `FK {PostsId: 4} FK {TagsId: 2}`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="fefdb-133">Visualizzazione estesa</span><span class="sxs-lookup"><span data-stu-id="fefdb-133">The long view</span></span>

<span data-ttu-id="fefdb-134">La visualizzazione estesa può essere inviata alla console in modo analogo alla visualizzazione breve:</span><span class="sxs-lookup"><span data-stu-id="fefdb-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="fefdb-135">L'output per lo stesso stato della vista breve precedente è:</span><span class="sxs-lookup"><span data-stu-id="fefdb-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="fefdb-136">Ogni entità rilevata e il relativo stato vengono visualizzati come prima.</span><span class="sxs-lookup"><span data-stu-id="fefdb-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="fefdb-137">Tuttavia, la visualizzazione Long Mostra anche i valori di proprietà e di navigazione.</span><span class="sxs-lookup"><span data-stu-id="fefdb-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="fefdb-138">Valori delle proprietà</span><span class="sxs-lookup"><span data-stu-id="fefdb-138">Property values</span></span>

<span data-ttu-id="fefdb-139">Per ogni proprietà, la visualizzazione estesa indica se la proprietà fa parte di una chiave primaria (PK), di una chiave alternativa (AK) o di una chiave esterna (FK).</span><span class="sxs-lookup"><span data-stu-id="fefdb-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="fefdb-140">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fefdb-140">For example:</span></span>

- <span data-ttu-id="fefdb-141">`Blog.Id` è una proprietà di chiave primaria: `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="fefdb-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="fefdb-142">`Blog.AssetsId` è una proprietà di chiave alternativa: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="fefdb-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="fefdb-143">`Post.BlogId` è una proprietà di chiave esterna: `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="fefdb-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="fefdb-144">`BlogAssets.Id` è una chiave primaria e una proprietà di chiave esterna: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="fefdb-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="fefdb-145">I valori delle proprietà modificati sono contrassegnati come tali e viene visualizzato anche il valore originale della proprietà.</span><span class="sxs-lookup"><span data-stu-id="fefdb-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="fefdb-146">Ad esempio: `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="fefdb-147">Infine, `Added` le entità con valori di chiave temporanei indicano che il valore è temporaneo.</span><span class="sxs-lookup"><span data-stu-id="fefdb-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="fefdb-148">Ad esempio: `Id: -2147482643 PK Temporary`.</span><span class="sxs-lookup"><span data-stu-id="fefdb-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="fefdb-149">Valori di navigazione</span><span class="sxs-lookup"><span data-stu-id="fefdb-149">Navigation values</span></span>

<span data-ttu-id="fefdb-150">I valori di navigazione vengono visualizzati usando i valori di chiave primaria delle entità a cui fanno riferimento gli spostamenti.</span><span class="sxs-lookup"><span data-stu-id="fefdb-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="fefdb-151">Nell'output precedente, ad esempio, post 3 è correlato al Blog 2.</span><span class="sxs-lookup"><span data-stu-id="fefdb-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="fefdb-152">Ciò significa che la `Post.Blog` navigazione punta all' `Blog` istanza con ID 2.</span><span class="sxs-lookup"><span data-stu-id="fefdb-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="fefdb-153">Questa operazione viene mostrata come `Blog: {Id: 2}` .</span><span class="sxs-lookup"><span data-stu-id="fefdb-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="fefdb-154">Lo stesso accade per le esplorazioni della raccolta, tranne per il fatto che in questo caso possono essere presenti più entità correlate.</span><span class="sxs-lookup"><span data-stu-id="fefdb-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="fefdb-155">Ad esempio, la navigazione della raccolta `Blog.Posts` contiene tre entità, con i valori di chiave 1, 2 e-2147482643 rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="fefdb-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="fefdb-156">Questa operazione viene mostrata come `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .</span><span class="sxs-lookup"><span data-stu-id="fefdb-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="fefdb-157">Registrazione del rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="fefdb-157">Change tracker logging</span></span>

<span data-ttu-id="fefdb-158">Il rilevamento modifiche registra i messaggi in `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> ogni volta che [rileva le modifiche alla proprietà o alla navigazione](xref:core/change-tracking/debug-views).</span><span class="sxs-lookup"><span data-stu-id="fefdb-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="fefdb-159">Ad esempio, quando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> viene chiamato nel codice all'inizio di questo documento e la [registrazione del debug è abilitata](xref:core/logging-events-diagnostics/index), vengono generati i log seguenti:</span><span class="sxs-lookup"><span data-stu-id="fefdb-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="fefdb-160">La tabella seguente riepiloga i messaggi di registrazione del rilevamento modifiche:</span><span class="sxs-lookup"><span data-stu-id="fefdb-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="fefdb-161">ID evento</span><span class="sxs-lookup"><span data-stu-id="fefdb-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="fefdb-162">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fefdb-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="fefdb-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> avvio in corso</span><span class="sxs-lookup"><span data-stu-id="fefdb-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="fefdb-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> completato</span><span class="sxs-lookup"><span data-stu-id="fefdb-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="fefdb-165">È stato modificato un valore normale della proprietà</span><span class="sxs-lookup"><span data-stu-id="fefdb-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="fefdb-166">È stato modificato un valore della proprietà di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="fefdb-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="fefdb-167">Per un'esplorazione della raccolta non Skip sono state aggiunte o rimosse entità correlate.</span><span class="sxs-lookup"><span data-stu-id="fefdb-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="fefdb-168">Una navigazione di riferimento è stata modificata in modo da puntare a un'altra entità o impostare su null</span><span class="sxs-lookup"><span data-stu-id="fefdb-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="fefdb-169">EF Core iniziato a tenere traccia di un'entità</span><span class="sxs-lookup"><span data-stu-id="fefdb-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="fefdb-170">Il valore <xref:Microsoft.EntityFrameworkCore.EntityState> di un'entità è stato modificato</span><span class="sxs-lookup"><span data-stu-id="fefdb-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="fefdb-171">È stato generato un valore per una proprietà</span><span class="sxs-lookup"><span data-stu-id="fefdb-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="fefdb-172">Per un'esplorazione della raccolta ignorata sono state aggiunte o rimosse entità correlate</span><span class="sxs-lookup"><span data-stu-id="fefdb-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="fefdb-173">Il modello</span><span class="sxs-lookup"><span data-stu-id="fefdb-173">The model</span></span>

<span data-ttu-id="fefdb-174">Il modello usato per gli esempi precedenti contiene i tipi di entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="fefdb-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="fefdb-175">Il modello viene configurato principalmente per convenzione, con poche righe in OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="fefdb-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
