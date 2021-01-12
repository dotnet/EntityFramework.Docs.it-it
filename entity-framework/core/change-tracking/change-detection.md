---
title: Rilevamento delle modifiche e notifiche-EF Core
description: Rilevamento delle modifiche alle proprietà e alle relazioni mediante DetectChanges o notifiche
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129732"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="98056-103">Rilevamento delle modifiche e notifiche</span><span class="sxs-lookup"><span data-stu-id="98056-103">Change Detection and Notifications</span></span>

<span data-ttu-id="98056-104">Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità.</span><span class="sxs-lookup"><span data-stu-id="98056-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="98056-105">Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="98056-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="98056-106">Questa operazione viene illustrata in [Rilevamento modifiche EF core e in](xref:core/change-tracking/index)questo documento si presuppone che gli Stati dell'entità e le nozioni di base del rilevamento delle modifiche Entity Framework Core (EF Core) siano compresi.</span><span class="sxs-lookup"><span data-stu-id="98056-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="98056-107">Per il rilevamento delle modifiche alle proprietà e alle relazioni è necessario che DbContext sia in grado di rilevare tali modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="98056-108">In questo documento viene illustrato il modo in cui si verifica questo rilevamento, nonché come utilizzare le notifiche delle proprietà o i proxy di rilevamento delle modifiche per forzare il rilevamento immediato delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="98056-109">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span><span class="sxs-lookup"><span data-stu-id="98056-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="98056-110">Rilevamento modifiche basato su snapshot</span><span class="sxs-lookup"><span data-stu-id="98056-110">Snapshot change tracking</span></span>

<span data-ttu-id="98056-111">Per impostazione predefinita, EF Core crea uno snapshot dei valori delle proprietà di ogni entità quando viene rilevata per la prima volta da un'istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="98056-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="98056-112">I valori archiviati in questo snapshot vengono quindi confrontati con i valori correnti dell'entità per determinare quali valori della proprietà sono stati modificati.</span><span class="sxs-lookup"><span data-stu-id="98056-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="98056-113">Questo rilevamento delle modifiche si verifica quando viene chiamato SaveChanges per assicurarsi che tutti i valori modificati vengano rilevati prima di inviare aggiornamenti al database.</span><span class="sxs-lookup"><span data-stu-id="98056-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="98056-114">Tuttavia, il rilevamento delle modifiche avviene anche in altri momenti per garantire che l'applicazione funzioni con informazioni di rilevamento aggiornate.</span><span class="sxs-lookup"><span data-stu-id="98056-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="98056-115">Il rilevamento delle modifiche può essere forzato in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98056-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="98056-116">Quando è necessario il rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="98056-116">When change detection is needed</span></span>

<span data-ttu-id="98056-117">Il rilevamento delle modifiche è necessario quando una proprietà o una navigazione è stata modificata _senza usare EF core per apportare questa modifica_.</span><span class="sxs-lookup"><span data-stu-id="98056-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="98056-118">Si consideri ad esempio il caricamento di Blog e post e quindi la modifica di queste entità:</span><span class="sxs-lookup"><span data-stu-id="98056-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="98056-119">Esaminando la [visualizzazione di debug di rilevamento modifiche](xref:core/change-tracking/debug-views) prima di chiamare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> viene indicato che le modifiche apportate non sono state rilevate e pertanto non vengono riflesse negli Stati dell'entità e i dati delle proprietà modificati:</span><span class="sxs-lookup"><span data-stu-id="98056-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="98056-120">In particolare, lo stato della voce di Blog è ancora `Unchanged` e il nuovo post non viene visualizzato come entità rilevata.</span><span class="sxs-lookup"><span data-stu-id="98056-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="98056-121">Si noterà che le proprietà segnalano i nuovi valori, anche se queste modifiche non sono state ancora rilevate dal EF Core.</span><span class="sxs-lookup"><span data-stu-id="98056-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="98056-122">Ciò è dovuto al fatto che la visualizzazione debug sta leggendo i valori correnti direttamente dall'istanza dell'entità.</span><span class="sxs-lookup"><span data-stu-id="98056-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="98056-123">A differenza di quanto segue con la visualizzazione debug dopo la chiamata a DetectChanges:</span><span class="sxs-lookup"><span data-stu-id="98056-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="98056-124">Il Blog è ora contrassegnato correttamente come `Modified` e il nuovo post è stato rilevato e viene registrato come `Added` .</span><span class="sxs-lookup"><span data-stu-id="98056-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="98056-125">All'inizio di questa sezione è stato indicato che è necessario rilevare le modifiche quando non si usa _EF core per apportare la modifica_.</span><span class="sxs-lookup"><span data-stu-id="98056-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="98056-126">Questo è ciò che accade nel codice riportato sopra.</span><span class="sxs-lookup"><span data-stu-id="98056-126">This is what is happening in the code above.</span></span> <span data-ttu-id="98056-127">In altre circostanze, le modifiche apportate alla proprietà e alla navigazione vengono apportate _direttamente nelle istanze di entità_ e non usando metodi EF core.</span><span class="sxs-lookup"><span data-stu-id="98056-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="98056-128">A differenza di quanto avviene con il codice seguente, che modifica le entità nello stesso modo, ma questa volta usando EF Core metodi:</span><span class="sxs-lookup"><span data-stu-id="98056-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="98056-129">In questo caso, la visualizzazione di debug del rilevamento modifiche mostra che tutti gli Stati delle entità e le modifiche delle proprietà sono noti, anche se non si è verificato alcun rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="98056-130">Questo è dovuto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> al fatto che è un metodo di EF core, il che significa che EF Core sa immediatamente le modifiche apportate da questo metodo.</span><span class="sxs-lookup"><span data-stu-id="98056-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="98056-131">Analogamente, <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> la chiamata a consente EF Core di conoscere immediatamente la nuova entità e di tenerne traccia in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="98056-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="98056-132">Non tentare di evitare di rilevare le modifiche usando sempre EF Core metodi per apportare modifiche alle entità.</span><span class="sxs-lookup"><span data-stu-id="98056-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="98056-133">Questa operazione è spesso più complessa ed esegue una minore quantità di modifiche alle entità in modo normale.</span><span class="sxs-lookup"><span data-stu-id="98056-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="98056-134">Lo scopo di questo documento è fornire informazioni sul momento in cui è necessario rilevare le modifiche e in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="98056-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="98056-135">L'intenzione non è incoraggiare la prevenzione del rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="98056-136">Metodi che rilevano automaticamente le modifiche</span><span class="sxs-lookup"><span data-stu-id="98056-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="98056-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> viene chiamato automaticamente dai metodi in cui è probabile che questa operazione abbia un effetto sui risultati.</span><span class="sxs-lookup"><span data-stu-id="98056-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="98056-138">Questi metodi sono:</span><span class="sxs-lookup"><span data-stu-id="98056-138">These methods are:</span></span>

- <span data-ttu-id="98056-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>per assicurarsi che tutte le modifiche vengano rilevate prima di aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="98056-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="98056-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>per assicurarsi che gli Stati delle entità e le proprietà modificate siano aggiornate.</span><span class="sxs-lookup"><span data-stu-id="98056-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="98056-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, per assicurarsi che il risultato sia accurato.</span><span class="sxs-lookup"><span data-stu-id="98056-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="98056-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, per garantire la correttezza degli Stati delle entità per le entità principale/padre prima della propagazione.</span><span class="sxs-lookup"><span data-stu-id="98056-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="98056-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, per assicurarsi che il grafico rilevato sia aggiornato.</span><span class="sxs-lookup"><span data-stu-id="98056-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="98056-144">Esistono anche alcune posizioni in cui il rilevamento delle modifiche avviene solo in una singola istanza di entità, anziché nell'intero grafico delle entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="98056-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="98056-145">Questi punti sono:</span><span class="sxs-lookup"><span data-stu-id="98056-145">These places are:</span></span>

- <span data-ttu-id="98056-146">Quando <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> si usa, per assicurarsi che lo stato dell'entità e le proprietà modificate siano aggiornate.</span><span class="sxs-lookup"><span data-stu-id="98056-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="98056-147">Quando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> si usano metodi come `Property` , `Collection` `Reference` o `Member` per assicurarsi che le modifiche delle proprietà, i valori correnti e così via siano aggiornati.</span><span class="sxs-lookup"><span data-stu-id="98056-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="98056-148">Quando un'entità dipendente/figlio sta per essere eliminata perché una relazione obbligatoria è stata interrotta.</span><span class="sxs-lookup"><span data-stu-id="98056-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="98056-149">Questa operazione rileva quando un'entità non deve essere eliminata perché è stata riassegnata all'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="98056-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="98056-150">Il rilevamento locale delle modifiche per una singola entità può essere attivato in modo esplicito chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98056-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="98056-151">Le modifiche rilevate localmente possono mancare alcune modifiche individuate da un rilevamento completo.</span><span class="sxs-lookup"><span data-stu-id="98056-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="98056-152">Ciò si verifica quando le azioni di propagazione derivanti da modifiche non rilevate ad altre entità hanno un effetto sull'entità in questione.</span><span class="sxs-lookup"><span data-stu-id="98056-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="98056-153">In tali situazioni è possibile che l'applicazione debba forzare un'analisi completa di tutte le entità chiamando in modo esplicito <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98056-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="98056-154">Disabilitazione del rilevamento delle modifiche automatico</span><span class="sxs-lookup"><span data-stu-id="98056-154">Disabling automatic change detection</span></span>

<span data-ttu-id="98056-155">Le prestazioni del rilevamento delle modifiche non costituiscono un collo di bottiglia per la maggior parte delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="98056-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="98056-156">Tuttavia, il rilevamento delle modifiche può costituire un problema di prestazioni per alcune applicazioni che tengono traccia di migliaia di entità.</span><span class="sxs-lookup"><span data-stu-id="98056-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="98056-157">Il numero esatto dipenderà da molti elementi, ad esempio il numero di proprietà nell'entità. Per questo motivo è possibile disabilitare il rilevamento automatico delle modifiche usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98056-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="98056-158">Si consideri, ad esempio, l'elaborazione di entità di join in una relazione molti-a-molti con payload:</span><span class="sxs-lookup"><span data-stu-id="98056-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="98056-159">Come è noto nella sezione precedente, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> rilevano automaticamente le modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="98056-160">Tuttavia, dopo aver chiamato le voci, il codice non esegue alcuna modifica dello stato dell'entità o della proprietà.</span><span class="sxs-lookup"><span data-stu-id="98056-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="98056-161">L'impostazione dei normali valori delle proprietà nelle entità aggiunte non comporta alcuna modifica dello stato. Il codice Disabilita pertanto il rilevamento automatico delle modifiche superflue quando si chiama il metodo SaveChanges di base.</span><span class="sxs-lookup"><span data-stu-id="98056-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="98056-162">Il codice utilizza inoltre un blocco try/finally per assicurarsi che l'impostazione predefinita venga ripristinata anche se SaveChanges ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="98056-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="98056-163">Non presupporre che il codice debba disabilitare il rilevamento automatico delle modifiche in per ottenere prestazioni ottimali.</span><span class="sxs-lookup"><span data-stu-id="98056-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="98056-164">Questa operazione è necessaria solo quando si esegue la profilatura di un'applicazione che monitora molte entità indica che le prestazioni del rilevamento delle modifiche sono un problema.</span><span class="sxs-lookup"><span data-stu-id="98056-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="98056-165">Rilevamento delle modifiche e delle conversioni dei valori</span><span class="sxs-lookup"><span data-stu-id="98056-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="98056-166">Per usare il rilevamento delle modifiche dello snapshot con un tipo di entità, EF Core deve essere in grado di:</span><span class="sxs-lookup"><span data-stu-id="98056-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="98056-167">Crea uno snapshot di ogni valore di proprietà quando viene rilevata l'entità</span><span class="sxs-lookup"><span data-stu-id="98056-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="98056-168">Confrontare questo valore con il valore corrente della proprietà</span><span class="sxs-lookup"><span data-stu-id="98056-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="98056-169">Genera un codice hash per il valore</span><span class="sxs-lookup"><span data-stu-id="98056-169">Generate a hash code for the value</span></span>

<span data-ttu-id="98056-170">Questa operazione viene gestita automaticamente da EF Core per i tipi che possono essere mappati direttamente al database.</span><span class="sxs-lookup"><span data-stu-id="98056-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="98056-171">Tuttavia, quando un [convertitore di valori viene usato per eseguire il mapping di una proprietà](xref:core/modeling/value-conversions), il convertitore deve specificare come eseguire queste azioni.</span><span class="sxs-lookup"><span data-stu-id="98056-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="98056-172">Questa operazione viene eseguita con un operatore di confronto del valore ed è descritta in dettaglio nella documentazione relativa agli [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="98056-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="98056-173">Entità di notifica</span><span class="sxs-lookup"><span data-stu-id="98056-173">Notification entities</span></span>

<span data-ttu-id="98056-174">Il rilevamento delle modifiche dello snapshot è consigliato per la maggior parte delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="98056-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="98056-175">Tuttavia, le applicazioni che tengono traccia di molte entità e/o apportano molte modifiche a tali entità possono trarre vantaggio dall'implementazione di entità che notificano automaticamente EF Core quando cambiano i valori di proprietà e di navigazione.</span><span class="sxs-lookup"><span data-stu-id="98056-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="98056-176">Queste sono note come "entità di notifica".</span><span class="sxs-lookup"><span data-stu-id="98056-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="98056-177">Implementazione di entità di notifica</span><span class="sxs-lookup"><span data-stu-id="98056-177">Implementing notification entities</span></span>

<span data-ttu-id="98056-178">Le entità di notifica utilizzano le <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfacce e, che fanno parte della libreria di classi base .NET (BCL).</span><span class="sxs-lookup"><span data-stu-id="98056-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="98056-179">Queste interfacce definiscono gli eventi che devono essere generati prima e dopo la modifica del valore di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="98056-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="98056-180">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="98056-181">Inoltre, tutte le esplorazioni della raccolta devono implementare `INotifyCollectionChanged` ; nell'esempio precedente questa operazione è stata soddisfatta usando un <xref:System.Collections.ObjectModel.ObservableCollection%601> di post.</span><span class="sxs-lookup"><span data-stu-id="98056-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="98056-182">EF Core fornisce anche un' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementazione con ricerche più efficienti a scapito dell'ordinamento stabile.</span><span class="sxs-lookup"><span data-stu-id="98056-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="98056-183">La maggior parte di questo codice di notifica viene in genere spostata in una classe di base non mappata.</span><span class="sxs-lookup"><span data-stu-id="98056-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="98056-184">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="98056-185">Configurazione di entità di notifica</span><span class="sxs-lookup"><span data-stu-id="98056-185">Configuring notification entities</span></span>

<span data-ttu-id="98056-186">Non esiste alcun modo per convalidare la EF Core che `INotifyPropertyChanging` o `INotifyPropertyChanged` siano completamente implementati per l'uso con EF core.</span><span class="sxs-lookup"><span data-stu-id="98056-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="98056-187">In particolare, alcuni utilizzi di queste interfacce vengono eseguite con le notifiche solo su determinate proprietà, anziché su tutte le proprietà (incluse le navigazioni) come richiesto dal EF Core.</span><span class="sxs-lookup"><span data-stu-id="98056-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="98056-188">Per questo motivo, EF Core non viene collegato automaticamente a questi eventi.</span><span class="sxs-lookup"><span data-stu-id="98056-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="98056-189">Al contrario, EF Core necessario configurare per l'utilizzo di queste entità di notifica.</span><span class="sxs-lookup"><span data-stu-id="98056-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="98056-190">Questa operazione viene in genere eseguita per tutti i tipi di entità chiamando <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98056-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="98056-191">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="98056-192">La strategia può essere impostata anche in modo diverso per diversi tipi di entità utilizzando <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> , ma ciò è in genere controproducente perché DetectChanges è ancora necessario per i tipi che non sono entità di notifica.</span><span class="sxs-lookup"><span data-stu-id="98056-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="98056-193">Per il rilevamento delle modifiche delle notifiche complete è necessario implementare sia che `INotifyPropertyChanging` `INotifyPropertyChanged` .</span><span class="sxs-lookup"><span data-stu-id="98056-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="98056-194">In questo modo è possibile salvare i valori originali immediatamente prima della modifica del valore della proprietà, evitando la necessità di EF Core creare uno snapshot durante il rilevamento dell'entità.</span><span class="sxs-lookup"><span data-stu-id="98056-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="98056-195">I tipi di entità che implementano solo `INotifyPropertyChanged` possono essere usati anche con EF core.</span><span class="sxs-lookup"><span data-stu-id="98056-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="98056-196">In questo caso, EF crea ancora uno snapshot durante il rilevamento di un'entità per tenere traccia dei valori originali, ma usa le notifiche per rilevare immediatamente le modifiche, anziché dover chiamare DetectChanges.</span><span class="sxs-lookup"><span data-stu-id="98056-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="98056-197">I diversi <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valori sono riepilogati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="98056-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="98056-198">ChangeTrackingStrategy</span><span class="sxs-lookup"><span data-stu-id="98056-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="98056-199">Interfacce necessarie</span><span class="sxs-lookup"><span data-stu-id="98056-199">Interfaces needed</span></span>                                      | <span data-ttu-id="98056-200">Richiede DetectChanges</span><span class="sxs-lookup"><span data-stu-id="98056-200">Needs DetectChanges</span></span> | <span data-ttu-id="98056-201">Snapshot dei valori originali</span><span class="sxs-lookup"><span data-stu-id="98056-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="98056-202">Snapshot</span><span class="sxs-lookup"><span data-stu-id="98056-202">Snapshot</span></span>                                            | <span data-ttu-id="98056-203">nessuno</span><span class="sxs-lookup"><span data-stu-id="98056-203">None</span></span>                                                   | <span data-ttu-id="98056-204">Sì</span><span class="sxs-lookup"><span data-stu-id="98056-204">Yes</span></span>                 | <span data-ttu-id="98056-205">Sì</span><span class="sxs-lookup"><span data-stu-id="98056-205">Yes</span></span>
| <span data-ttu-id="98056-206">ChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="98056-206">ChangedNotifications</span></span>                                | <span data-ttu-id="98056-207">INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="98056-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="98056-208">No</span><span class="sxs-lookup"><span data-stu-id="98056-208">No</span></span>                  | <span data-ttu-id="98056-209">Sì</span><span class="sxs-lookup"><span data-stu-id="98056-209">Yes</span></span>
| <span data-ttu-id="98056-210">ChangingAndChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="98056-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="98056-211">INotifyPropertyChanged e INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="98056-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="98056-212">No</span><span class="sxs-lookup"><span data-stu-id="98056-212">No</span></span>                  | <span data-ttu-id="98056-213">No</span><span class="sxs-lookup"><span data-stu-id="98056-213">No</span></span>
| <span data-ttu-id="98056-214">ChangingAndChangedNotificationsWithOriginalValues</span><span class="sxs-lookup"><span data-stu-id="98056-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="98056-215">INotifyPropertyChanged e INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="98056-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="98056-216">No</span><span class="sxs-lookup"><span data-stu-id="98056-216">No</span></span>                  | <span data-ttu-id="98056-217">Sì</span><span class="sxs-lookup"><span data-stu-id="98056-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="98056-218">Utilizzo di entità di notifica</span><span class="sxs-lookup"><span data-stu-id="98056-218">Using notification entities</span></span>

<span data-ttu-id="98056-219">Le entità di notifica si comportano come qualsiasi altra entità, ad eccezione del fatto che le modifiche apportate alle istanze di entità non richiedono una chiamata a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> per rilevare tali modifiche.</span><span class="sxs-lookup"><span data-stu-id="98056-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="98056-220">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="98056-221">Con le entità normali, la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) ha mostrato che queste modifiche non sono state rilevate fino a quando non è stato chiamato DetectChanges.</span><span class="sxs-lookup"><span data-stu-id="98056-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="98056-222">Esaminando la visualizzazione debug quando vengono usate le entità di notifica, le modifiche sono state rilevate immediatamente:</span><span class="sxs-lookup"><span data-stu-id="98056-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="98056-223">Proxy di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="98056-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="98056-224">I proxy di rilevamento delle modifiche sono stati introdotti nella EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="98056-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="98056-225">EF Core possibile generare dinamicamente tipi proxy che implementano <xref:System.ComponentModel.INotifyPropertyChanging> e <xref:System.ComponentModel.INotifyPropertyChanged> .</span><span class="sxs-lookup"><span data-stu-id="98056-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="98056-226">È necessario installare il pacchetto NuGet [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitare i proxy di rilevamento delle modifiche con, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="98056-227">La creazione di un proxy dinamico comporta la creazione di un nuovo tipo .NET dinamico (usando l'implementazione dei proxy di [Castle. Core](https://www.nuget.org/packages/Castle.Core/) ), che eredita dal tipo di entità e quindi esegue l'override di tutti i setter di proprietà.</span><span class="sxs-lookup"><span data-stu-id="98056-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="98056-228">I tipi di entità per i proxy devono pertanto essere tipi che possono essere ereditati da e devono avere proprietà di cui è possibile eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="98056-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="98056-229">Inoltre, le esplorazioni delle raccolte create in modo esplicito devono implementare, <xref:System.Collections.Specialized.INotifyCollectionChanged> ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98056-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="98056-230">Uno svantaggio significativo dei proxy di rilevamento delle modifiche è che EF Core necessario rilevare sempre le istanze del proxy, mai le istanze del tipo di entità sottostante.</span><span class="sxs-lookup"><span data-stu-id="98056-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="98056-231">Questo è dovuto al fatto che le istanze del tipo di entità sottostante non genereranno le notifiche, il che significa che le modifiche apportate a tali entità verranno perse.</span><span class="sxs-lookup"><span data-stu-id="98056-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="98056-232">EF Core crea automaticamente istanze proxy quando si esegue una query sul database, quindi questo svantaggio è in genere limitato al rilevamento di nuove istanze di entità.</span><span class="sxs-lookup"><span data-stu-id="98056-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="98056-233">Queste istanze devono essere create usando i <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> metodi di estensione e **non** in modo normale usando `new` .</span><span class="sxs-lookup"><span data-stu-id="98056-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="98056-234">Questo significa che il codice degli esempi precedenti deve ora usare `CreateProxy` :</span><span class="sxs-lookup"><span data-stu-id="98056-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="98056-235">Eventi rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="98056-235">Change tracking events</span></span>

<span data-ttu-id="98056-236">EF Core genera l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> evento quando viene rilevata un'entità per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="98056-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="98056-237">Le modifiche dello stato dell'entità future generano <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> eventi.</span><span class="sxs-lookup"><span data-stu-id="98056-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="98056-238">Per ulteriori informazioni, vedere [eventi .NET in EF Core](xref:core/logging-events-diagnostics/events) .</span><span class="sxs-lookup"><span data-stu-id="98056-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="98056-239">L' `StateChanged` evento non viene generato quando viene rilevata per la prima volta un'entità, anche se lo stato è stato modificato da `Detached` a uno degli altri Stati.</span><span class="sxs-lookup"><span data-stu-id="98056-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="98056-240">Assicurarsi di restare in ascolto degli `StateChanged` eventi e per `Tracked` ottenere tutte le notifiche pertinenti.</span><span class="sxs-lookup"><span data-stu-id="98056-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
