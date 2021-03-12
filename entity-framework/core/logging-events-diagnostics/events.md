---
title: Eventi .NET-EF Core
description: Eventi .NET definiti da EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: c26032d19d7bc05d30d4576534c7425da4472072
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024238"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="1aad4-103">Eventi .NET in EF Core</span><span class="sxs-lookup"><span data-stu-id="1aad4-103">.NET Events in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="1aad4-104">È possibile [scaricare l'esempio di eventi](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) da GitHub.</span><span class="sxs-lookup"><span data-stu-id="1aad4-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="1aad4-105">Entity Framework Core (EF Core) espone [gli eventi .NET](/dotnet/standard/events/) da utilizzare come callback quando si verificano determinate operazioni nel codice EF core.</span><span class="sxs-lookup"><span data-stu-id="1aad4-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="1aad4-106">Gli eventi sono più semplici degli [intercettori](xref:core/logging-events-diagnostics/interceptors) e consentono una registrazione più flessibile.</span><span class="sxs-lookup"><span data-stu-id="1aad4-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="1aad4-107">Tuttavia, sono solo sincronizzati e pertanto non possono eseguire operazioni di I/O asincrone non bloccanti.</span><span class="sxs-lookup"><span data-stu-id="1aad4-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="1aad4-108">Gli eventi vengono registrati per ogni `DbContext` istanza.</span><span class="sxs-lookup"><span data-stu-id="1aad4-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="1aad4-109">Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.</span><span class="sxs-lookup"><span data-stu-id="1aad4-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="1aad4-110">Eventi generati da EF Core</span><span class="sxs-lookup"><span data-stu-id="1aad4-110">Events raised by EF Core</span></span>

<span data-ttu-id="1aad4-111">Gli eventi seguenti vengono generati da EF Core:</span><span class="sxs-lookup"><span data-stu-id="1aad4-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="1aad4-112">Evento</span><span class="sxs-lookup"><span data-stu-id="1aad4-112">Event</span></span> | <span data-ttu-id="1aad4-113">Versione introdotta</span><span class="sxs-lookup"><span data-stu-id="1aad4-113">Version introduced</span></span> | <span data-ttu-id="1aad4-114">Quando viene generato</span><span class="sxs-lookup"><span data-stu-id="1aad4-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="1aad4-115">5.0</span><span class="sxs-lookup"><span data-stu-id="1aad4-115">5.0</span></span> | <span data-ttu-id="1aad4-116">All'inizio di <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="1aad4-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="1aad4-117">5.0</span><span class="sxs-lookup"><span data-stu-id="1aad4-117">5.0</span></span> | <span data-ttu-id="1aad4-118">Alla fine di un'operazione riuscita <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="1aad4-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="1aad4-119">5.0</span><span class="sxs-lookup"><span data-stu-id="1aad4-119">5.0</span></span> | <span data-ttu-id="1aad4-120">Alla fine di un errore <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="1aad4-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="1aad4-121">2.1</span><span class="sxs-lookup"><span data-stu-id="1aad4-121">2.1</span></span> | <span data-ttu-id="1aad4-122">Quando un'entità viene rilevata dal contesto</span><span class="sxs-lookup"><span data-stu-id="1aad4-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="1aad4-123">2.1</span><span class="sxs-lookup"><span data-stu-id="1aad4-123">2.1</span></span> | <span data-ttu-id="1aad4-124">Modifica dello stato di un'entità rilevata</span><span class="sxs-lookup"><span data-stu-id="1aad4-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="1aad4-125">Esempio: modifiche allo stato del timestamp</span><span class="sxs-lookup"><span data-stu-id="1aad4-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="1aad4-126">Ogni entità rilevata da un DbContext dispone di un oggetto <xref:Microsoft.EntityFrameworkCore.EntityState> .</span><span class="sxs-lookup"><span data-stu-id="1aad4-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="1aad4-127">Lo stato indica, ad esempio, `Added` che l'entità verrà inserita nel database.</span><span class="sxs-lookup"><span data-stu-id="1aad4-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="1aad4-128">In questo esempio vengono usati gli <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> eventi e per rilevare la modifica dello stato di un'entità.</span><span class="sxs-lookup"><span data-stu-id="1aad4-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="1aad4-129">Viene quindi timbrata l'entità con l'ora corrente che indica quando si è verificata questa modifica.</span><span class="sxs-lookup"><span data-stu-id="1aad4-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="1aad4-130">Ciò comporta timestamp che indicano quando l'entità è stata inserita, eliminata e/o ultimo aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="1aad4-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="1aad4-131">I tipi di entità in questo esempio implementano un'interfaccia che definisce le proprietà timestamp:</span><span class="sxs-lookup"><span data-stu-id="1aad4-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="1aad4-132">Un metodo nel DbContext dell'applicazione può quindi impostare i timestamp per qualsiasi entità che implementa questa interfaccia:</span><span class="sxs-lookup"><span data-stu-id="1aad4-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

<span data-ttu-id="1aad4-133">Questo metodo ha la firma appropriata da usare come gestore eventi per gli `Tracked` `StateChanged` eventi e.</span><span class="sxs-lookup"><span data-stu-id="1aad4-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="1aad4-134">Il gestore viene registrato per entrambi gli eventi nel costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="1aad4-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="1aad4-135">Si noti che gli eventi possono essere associati a un DbContext in qualsiasi momento; non è necessario che questo avvenga nel costruttore di contesto.</span><span class="sxs-lookup"><span data-stu-id="1aad4-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="1aad4-136">Entrambi gli eventi sono necessari perché le nuove entità generano `Tracked` eventi quando vengono rilevati per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="1aad4-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="1aad4-137">`StateChanged` gli eventi vengono generati solo per le entità che cambiano lo stato mentre sono _già_ rilevate.</span><span class="sxs-lookup"><span data-stu-id="1aad4-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="1aad4-138">L' [esempio per questo esempio contiene](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) una semplice applicazione console che apporta modifiche al database blogging:</span><span class="sxs-lookup"><span data-stu-id="1aad4-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

<span data-ttu-id="1aad4-139">L'output di questo codice mostra le modifiche di stato che si verificano e i timestamp applicati:</span><span class="sxs-lookup"><span data-stu-id="1aad4-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
