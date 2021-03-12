---
title: Rilevamento modifiche EF Core
description: Panoramica del rilevamento delle modifiche per EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: ccd14947cf53e3f8a38d2e0ddf2601b29e845775
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024446"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="1b6c4-103">Rilevamento modifiche in EF Core</span><span class="sxs-lookup"><span data-stu-id="1b6c4-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="1b6c4-104">Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="1b6c4-105">Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="1b6c4-106">In questo documento viene presentata una panoramica del rilevamento delle modifiche Entity Framework Core (EF Core) e del modo in cui è correlato a query e aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="1b6c4-107">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="1b6c4-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="1b6c4-108">Per semplicità, questo documento usa e fa riferimento a metodi sincroni come, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> anziché ai relativi equivalenti asincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather than their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="1b6c4-109">La chiamata e l'attesa del metodo asincrono possono essere sostituite se non diversamente specificato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="1b6c4-110">Come tenere traccia delle entità</span><span class="sxs-lookup"><span data-stu-id="1b6c4-110">How to track entities</span></span>

<span data-ttu-id="1b6c4-111">Le istanze di entità vengono rilevate quando sono:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="1b6c4-112">Restituito da una query eseguita sul database</span><span class="sxs-lookup"><span data-stu-id="1b6c4-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="1b6c4-113">Associato in modo esplicito a DbContext da `Add` ,, `Attach` `Update` o metodi simili</span><span class="sxs-lookup"><span data-stu-id="1b6c4-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="1b6c4-114">Rilevato come nuove entità connesse a entità registrate esistenti</span><span class="sxs-lookup"><span data-stu-id="1b6c4-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="1b6c4-115">Le istanze di entità non vengono più rilevate quando:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="1b6c4-116">Il DbContext è stato eliminato</span><span class="sxs-lookup"><span data-stu-id="1b6c4-116">The DbContext is disposed</span></span>
- <span data-ttu-id="1b6c4-117">Il rilevamento modifiche è stato cancellato (EF Core 5,0 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="1b6c4-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="1b6c4-118">Le entità vengono scollegate in modo esplicito</span><span class="sxs-lookup"><span data-stu-id="1b6c4-118">The entities are explicitly detached</span></span>

<span data-ttu-id="1b6c4-119">DbContext è progettato per rappresentare un'unità di lavoro di breve durata, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1b6c4-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="1b6c4-120">Questo significa che l'eliminazione di DbContext è _il modo normale_ per arrestare il rilevamento delle entità.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="1b6c4-121">In altre parole, la durata di una DbContext deve essere:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="1b6c4-122">Creare l'istanza di DbContext</span><span class="sxs-lookup"><span data-stu-id="1b6c4-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="1b6c4-123">Tenere traccia di alcune entità</span><span class="sxs-lookup"><span data-stu-id="1b6c4-123">Track some entities</span></span>
3. <span data-ttu-id="1b6c4-124">Apportare alcune modifiche alle entità</span><span class="sxs-lookup"><span data-stu-id="1b6c4-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="1b6c4-125">Chiamare SaveChanges per aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="1b6c4-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="1b6c4-126">Eliminare l'istanza di DbContext</span><span class="sxs-lookup"><span data-stu-id="1b6c4-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="1b6c4-127">Quando si adotta questo approccio, non è necessario cancellare lo strumento di rilevamento delle modifiche o scollegare esplicitamente le istanze dell'entità.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="1b6c4-128">Tuttavia, se è necessario scollegare le entità, la chiamata a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> è più efficiente rispetto alla disconnessione delle entità una alla volta.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="1b6c4-129">Stati di entità</span><span class="sxs-lookup"><span data-stu-id="1b6c4-129">Entity states</span></span>

<span data-ttu-id="1b6c4-130">Ogni entità è associata a un oggetto specificato <xref:Microsoft.EntityFrameworkCore.EntityState> :</span><span class="sxs-lookup"><span data-stu-id="1b6c4-130">Every entity is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="1b6c4-131">`Detached` le entità non vengono rilevate da <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="1b6c4-132">`Added` le entità sono nuove e non sono ancora state inserite nel database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="1b6c4-133">Ciò significa che verranno inserite quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="1b6c4-134">`Unchanged` le entità _non_ sono state modificate dopo che sono state eseguite query dal database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="1b6c4-135">Tutte le entità restituite dalle query sono inizialmente in questo stato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="1b6c4-136">`Modified` le entità sono state modificate dopo l'interrogazione dal database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="1b6c4-137">Ciò significa che verranno aggiornati quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="1b6c4-138">`Deleted` le entità sono presenti nel database, ma sono contrassegnate per essere eliminate quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="1b6c4-139">EF Core tiene traccia delle modifiche a livello di proprietà.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="1b6c4-140">Se, ad esempio, viene modificato solo un valore di proprietà, un aggiornamento del database modificherà solo tale valore.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="1b6c4-141">Tuttavia, le proprietà possono essere contrassegnate solo come modificate quando l'entità stessa è nello stato modificato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="1b6c4-142">Oppure, da una prospettiva alternativa, lo stato modificato indica che almeno un valore della proprietà è stato contrassegnato come modificato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="1b6c4-143">Nella tabella seguente vengono riepilogati i diversi stati:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="1b6c4-144">Stato dell'entità</span><span class="sxs-lookup"><span data-stu-id="1b6c4-144">Entity state</span></span>     | <span data-ttu-id="1b6c4-145">Rilevato da DbContext</span><span class="sxs-lookup"><span data-stu-id="1b6c4-145">Tracked by DbContext</span></span> | <span data-ttu-id="1b6c4-146">Esistente nel database</span><span class="sxs-lookup"><span data-stu-id="1b6c4-146">Exists in database</span></span> | <span data-ttu-id="1b6c4-147">Proprietà modificate</span><span class="sxs-lookup"><span data-stu-id="1b6c4-147">Properties modified</span></span> | <span data-ttu-id="1b6c4-148">Azione su SaveChanges</span><span class="sxs-lookup"><span data-stu-id="1b6c4-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="1b6c4-149">No</span><span class="sxs-lookup"><span data-stu-id="1b6c4-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="1b6c4-150">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-150">Yes</span></span>                  | <span data-ttu-id="1b6c4-151">No</span><span class="sxs-lookup"><span data-stu-id="1b6c4-151">No</span></span>                 | -                   | <span data-ttu-id="1b6c4-152">Insert</span><span class="sxs-lookup"><span data-stu-id="1b6c4-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="1b6c4-153">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-153">Yes</span></span>                  | <span data-ttu-id="1b6c4-154">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-154">Yes</span></span>                | <span data-ttu-id="1b6c4-155">No</span><span class="sxs-lookup"><span data-stu-id="1b6c4-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="1b6c4-156">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-156">Yes</span></span>                  | <span data-ttu-id="1b6c4-157">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-157">Yes</span></span>                | <span data-ttu-id="1b6c4-158">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-158">Yes</span></span>                 | <span data-ttu-id="1b6c4-159">Aggiornamento</span><span class="sxs-lookup"><span data-stu-id="1b6c4-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="1b6c4-160">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-160">Yes</span></span>                  | <span data-ttu-id="1b6c4-161">Sì</span><span class="sxs-lookup"><span data-stu-id="1b6c4-161">Yes</span></span>                | -                   | <span data-ttu-id="1b6c4-162">Elimina</span><span class="sxs-lookup"><span data-stu-id="1b6c4-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="1b6c4-163">Questo testo usa termini di database relazionali per maggiore chiarezza.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="1b6c4-164">I database NoSQL in genere supportano operazioni simili, ma possibilmente con nomi diversi.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="1b6c4-165">Per ulteriori informazioni, consultare la documentazione del provider di database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="1b6c4-166">Rilevamento da query</span><span class="sxs-lookup"><span data-stu-id="1b6c4-166">Tracking from queries</span></span>

<span data-ttu-id="1b6c4-167">EF Core rilevamento delle modifiche funziona meglio quando la stessa <xref:Microsoft.EntityFrameworkCore.DbContext> istanza viene utilizzata per eseguire query per entità e aggiornarle chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="1b6c4-168">Questo perché EF Core rileva automaticamente lo stato delle entità sottoposte a query e quindi rileva eventuali modifiche apportate a tali entità quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="1b6c4-169">Questo approccio presenta diversi vantaggi rispetto alla [verifica esplicita delle istanze di entità](xref:core/change-tracking/explicit-tracking):</span><span class="sxs-lookup"><span data-stu-id="1b6c4-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="1b6c4-170">È semplice.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-170">It is simple.</span></span> <span data-ttu-id="1b6c4-171">Raramente gli Stati dell'entità devono essere modificati in modo esplicito. EF Core si occupa delle modifiche di stato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="1b6c4-172">Gli aggiornamenti sono limitati solo ai valori effettivamente modificati.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="1b6c4-173">I valori delle [proprietà shadow](xref:core/modeling/shadow-properties) vengono conservati e utilizzati in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="1b6c4-174">Questo è particolarmente importante quando le chiavi esterne vengono archiviate in stato di ombreggiatura.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="1b6c4-175">I valori originali delle proprietà vengono conservati automaticamente e usati per aggiornamenti efficienti.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="1b6c4-176">Semplice query e aggiornamento</span><span class="sxs-lookup"><span data-stu-id="1b6c4-176">Simple query and update</span></span>

<span data-ttu-id="1b6c4-177">Si consideri, ad esempio, un semplice modello di Blog/Post:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-177">For example, consider a simple blog/posts model:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="1b6c4-178">È possibile utilizzare questo modello per eseguire una query per i Blog e i post e quindi per apportare alcuni aggiornamenti al database:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="1b6c4-179">La chiamata a SaveChanges comporta gli aggiornamenti del database seguenti, usando SQLite come database di esempio:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="1b6c4-180">La [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) è un ottimo modo per visualizzare quali entità vengono registrate e quali sono gli Stati.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="1b6c4-181">Inserire, ad esempio, il codice seguente nell'esempio precedente prima di chiamare SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="1b6c4-182">Genera l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="1b6c4-183">Si noti in particolare:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-183">Notice specifically:</span></span>

- <span data-ttu-id="1b6c4-184">La `Blog.Name` proprietà è contrassegnata come modificata ( `Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'` ) e in questo modo il Blog si trova nello `Modified` stato.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="1b6c4-185">La `Post.Title` proprietà di post 2 è contrassegnata come modificata ( `Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'` ) e ciò comporta lo stato di questo post `Modified` .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="1b6c4-186">Gli altri valori di proprietà di post 2 non sono stati modificati e pertanto non sono contrassegnati come modificati.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="1b6c4-187">Questo è il motivo per cui questi valori non sono inclusi nell'aggiornamento del database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="1b6c4-188">L'altro post non è stato modificato in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-188">The other post was not modified in any way.</span></span> <span data-ttu-id="1b6c4-189">Questo è il motivo per cui si trova ancora nello `Unchanged` stato e non è incluso nell'aggiornamento del database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="1b6c4-190">Eseguire una query e inserire, aggiornare ed eliminare</span><span class="sxs-lookup"><span data-stu-id="1b6c4-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="1b6c4-191">Gli aggiornamenti come quelli nell'esempio precedente possono essere combinati con inserimenti ed eliminazioni nella stessa unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="1b6c4-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="1b6c4-193">In questo esempio:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-193">In this example:</span></span>

- <span data-ttu-id="1b6c4-194">Un blog e i post correlati vengono sottoposti a query dal database e monitorati</span><span class="sxs-lookup"><span data-stu-id="1b6c4-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="1b6c4-195">La `Blog.Name` proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="1b6c4-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="1b6c4-196">Viene aggiunto un nuovo post alla raccolta dei post esistenti per il Blog</span><span class="sxs-lookup"><span data-stu-id="1b6c4-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="1b6c4-197">Un post esistente è contrassegnato per l'eliminazione chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="1b6c4-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="1b6c4-198">Esaminare di nuovo la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges indica il modo in cui EF Core tiene traccia delle modifiche:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
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
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="1b6c4-199">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-199">Notice that:</span></span>

- <span data-ttu-id="1b6c4-200">Il Blog è contrassegnato come `Modified` .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="1b6c4-201">Verrà generato un aggiornamento del database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-201">This will generate a database update.</span></span>
- <span data-ttu-id="1b6c4-202">Il post 2 è contrassegnato come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="1b6c4-203">Verrà generata un'eliminazione del database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-203">This will generate a database delete.</span></span>
- <span data-ttu-id="1b6c4-204">Un nuovo post con un ID temporaneo è associato al Blog 1 ed è contrassegnato come `Added` .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="1b6c4-205">Verrà generato un inserimento di database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-205">This will generate a database insert.</span></span>

<span data-ttu-id="1b6c4-206">In questo modo vengono restituiti i comandi di database seguenti (usando SQLite) quando viene chiamato SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="1b6c4-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="1b6c4-207">Per ulteriori informazioni sull'inserimento e l'eliminazione di entità, vedere [Rilevamento esplicito delle entità](xref:core/change-tracking/explicit-tracking) .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="1b6c4-208">Per ulteriori informazioni su come EF Core rileva automaticamente le modifiche come questa, vedere rilevamento delle modifiche [e notifiche](xref:core/change-tracking/change-detection) .</span><span class="sxs-lookup"><span data-stu-id="1b6c4-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="1b6c4-209">Chiamare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> per determinare se sono state apportate modifiche che determineranno l'aggiornamento del SaveChanges al database.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="1b6c4-210">Se HasChanges restituisce false, SaveChanges sarà un no-op.</span><span class="sxs-lookup"><span data-stu-id="1b6c4-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
