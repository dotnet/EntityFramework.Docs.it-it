---
title: Accesso alle entità rilevate-EF Core
description: Uso di EntityEntry, DbContext. entrys e DbSet. local per accedere alle entità registrate
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129735"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="7936f-103">Accesso alle entità rilevate</span><span class="sxs-lookup"><span data-stu-id="7936f-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="7936f-104">Sono disponibili quattro API principali per l'accesso alle entità registrate da un <xref:Microsoft.EntityFrameworkCore.DbContext> :</span><span class="sxs-lookup"><span data-stu-id="7936f-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="7936f-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> Restituisce un' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> istanza per un'istanza di entità specificata.</span><span class="sxs-lookup"><span data-stu-id="7936f-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="7936f-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> restituisce <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> le istanze per tutte le entità rilevate o per tutte le entità rilevate di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="7936f-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="7936f-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> trovano una singola entità in base alla chiave primaria, esaminando prima di tutto le entità rilevate, quindi eseguendo una query sul database, se necessario.</span><span class="sxs-lookup"><span data-stu-id="7936f-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="7936f-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Restituisce le entità effettive (non le istanze EntityEntry) per le entità del tipo di entità rappresentato da DbSet.</span><span class="sxs-lookup"><span data-stu-id="7936f-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="7936f-109">Ognuno di questi elementi viene descritto più dettagliatamente nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="7936f-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="7936f-110">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="7936f-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="7936f-111">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="7936f-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="7936f-112">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span><span class="sxs-lookup"><span data-stu-id="7936f-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="7936f-113">Uso di istanze di DbContext. entry e EntityEntry</span><span class="sxs-lookup"><span data-stu-id="7936f-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="7936f-114">Per ogni entità rilevata, Entity Framework Core (EF Core) tiene traccia di:</span><span class="sxs-lookup"><span data-stu-id="7936f-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="7936f-115">Stato generale dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-115">The overall state of the entity.</span></span> <span data-ttu-id="7936f-116">Questo è uno tra `Unchanged` , `Modified` , `Added` o. `Deleted` per ulteriori informazioni, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="7936f-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="7936f-117">Relazioni tra le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="7936f-117">The relationships between tracked entities.</span></span> <span data-ttu-id="7936f-118">Ad esempio, il Blog a cui appartiene un post.</span><span class="sxs-lookup"><span data-stu-id="7936f-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="7936f-119">Valori correnti delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-119">The "current values" of properties.</span></span>
- <span data-ttu-id="7936f-120">I "valori originali" delle proprietà, quando queste informazioni sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="7936f-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="7936f-121">I valori originali sono i valori delle proprietà esistenti quando è stata eseguita una query sull'entità dal database.</span><span class="sxs-lookup"><span data-stu-id="7936f-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="7936f-122">I valori delle proprietà sono stati modificati da quando sono stati sottoposti a query.</span><span class="sxs-lookup"><span data-stu-id="7936f-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="7936f-123">Altre informazioni sui valori delle proprietà, ad esempio se il valore è [temporaneo](xref:core/change-tracking/miscellaneous#temporary-values)o meno.</span><span class="sxs-lookup"><span data-stu-id="7936f-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="7936f-124">Se si passa un'istanza di entità a <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> , si ottiene un oggetto che <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> fornisce accesso a queste informazioni per l'entità specificata.</span><span class="sxs-lookup"><span data-stu-id="7936f-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="7936f-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="7936f-126">Le sezioni seguenti illustrano come usare un EntityEntry per accedere e modificare lo stato dell'entità, nonché lo stato delle proprietà e degli spostamenti dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="7936f-127">Utilizzo dell'entità</span><span class="sxs-lookup"><span data-stu-id="7936f-127">Working with the entity</span></span>

<span data-ttu-id="7936f-128">L'utilizzo più comune di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> è l'accesso alla corrente <xref:Microsoft.EntityFrameworkCore.EntityState> di un'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="7936f-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="7936f-130">Il metodo Entry può essere usato anche su entità non ancora rilevate.</span><span class="sxs-lookup"><span data-stu-id="7936f-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="7936f-131">Questa operazione _non avvia il rilevamento dell'entità_; lo stato dell'entità è ancora `Detatched` .</span><span class="sxs-lookup"><span data-stu-id="7936f-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="7936f-132">Tuttavia, il EntityEntry restituito può essere usato per modificare lo stato dell'entità, a quel punto l'entità verrà rilevata nello stato specificato.</span><span class="sxs-lookup"><span data-stu-id="7936f-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="7936f-133">Il codice seguente, ad esempio, avvierà il rilevamento di un'istanza di Blog come `Added` :</span><span class="sxs-lookup"><span data-stu-id="7936f-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="7936f-134">Diversamente da quanto avviene in EF6, l'impostazione dello stato di una singola entità non comporterà il rilevamento di tutte le entità connesse.</span><span class="sxs-lookup"><span data-stu-id="7936f-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="7936f-135">In questo modo si imposta lo stato come operazione di livello inferiore rispetto alla chiamata a `Add` , `Attach` o `Update` , che opera su un intero grafico di entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="7936f-136">La tabella seguente riepiloga i modi in cui usare un EntityEntry per lavorare con un'intera entità:</span><span class="sxs-lookup"><span data-stu-id="7936f-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="7936f-137">Membro EntityEntry</span><span class="sxs-lookup"><span data-stu-id="7936f-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="7936f-138">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7936f-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="7936f-139">Ottiene e imposta l'oggetto <xref:Microsoft.EntityFrameworkCore.EntityState> dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="7936f-140">Ottiene l'istanza dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="7936f-141">Oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> che tiene traccia dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="7936f-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="7936f-143">Indica se per l'entità è stato impostato il valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="7936f-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="7936f-144">Sovrascrive i valori di proprietà con i valori letti dal database.</span><span class="sxs-lookup"><span data-stu-id="7936f-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="7936f-145">Forza il rilevamento delle modifiche solo per questa entità. vedere [rilevamento delle modifiche e notifiche](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="7936f-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="7936f-146">Utilizzo di una singola proprietà</span><span class="sxs-lookup"><span data-stu-id="7936f-146">Working with a single property</span></span>

<span data-ttu-id="7936f-147">Diversi overload di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> consentono l'accesso alle informazioni relative a una singola proprietà di un'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="7936f-148">Ad esempio, usando un'API di tipo Fluent fortemente tipizzata:</span><span class="sxs-lookup"><span data-stu-id="7936f-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="7936f-149">Il nome della proprietà può invece essere passato come stringa.</span><span class="sxs-lookup"><span data-stu-id="7936f-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="7936f-150">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="7936f-151">Il restituito <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> può quindi essere utilizzato per accedere alle informazioni sulla proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="7936f-152">Ad esempio, può essere usato per ottenere e impostare il valore corrente della proprietà in questa entità:</span><span class="sxs-lookup"><span data-stu-id="7936f-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="7936f-153">Entrambi i metodi di proprietà usati sopra restituiscono un'istanza generica fortemente tipizzata <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> .</span><span class="sxs-lookup"><span data-stu-id="7936f-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="7936f-154">L'uso di questo tipo generico è preferibile perché consente l'accesso ai valori delle proprietà senza i [tipi di valore Boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span><span class="sxs-lookup"><span data-stu-id="7936f-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="7936f-155">Tuttavia, se il tipo di entità o proprietà non è noto in fase di compilazione, è possibile ottenere invece un oggetto non generico <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> :</span><span class="sxs-lookup"><span data-stu-id="7936f-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="7936f-156">Questo consente l'accesso alle informazioni sulle proprietà per qualsiasi proprietà indipendentemente dal tipo, a scapito dei tipi di valore Boxing.</span><span class="sxs-lookup"><span data-stu-id="7936f-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="7936f-157">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="7936f-158">Nella tabella seguente sono riepilogate le informazioni sulle proprietà esposte da PropertyEntry:</span><span class="sxs-lookup"><span data-stu-id="7936f-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="7936f-159">Membro PropertyEntry</span><span class="sxs-lookup"><span data-stu-id="7936f-159">PropertyEntry member</span></span>                               | <span data-ttu-id="7936f-160">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7936f-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="7936f-161">Ottiene e imposta il valore corrente della proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="7936f-162">Ottiene e imposta il valore originale della proprietà, se disponibile.</span><span class="sxs-lookup"><span data-stu-id="7936f-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="7936f-163">Un riferimento all'oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> per l'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="7936f-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadati per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="7936f-165">Indica se questa proprietà è contrassegnata come modificata e consente la modifica di questo stato.</span><span class="sxs-lookup"><span data-stu-id="7936f-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="7936f-166">Indica se questa proprietà è contrassegnata come [temporanea](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)e consente la modifica di questo stato.</span><span class="sxs-lookup"><span data-stu-id="7936f-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="7936f-167">Note:</span><span class="sxs-lookup"><span data-stu-id="7936f-167">Notes:</span></span>

- <span data-ttu-id="7936f-168">Il valore originale di una proprietà è il valore che la proprietà aveva quando l'entità veniva sottoposta a query dal database.</span><span class="sxs-lookup"><span data-stu-id="7936f-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="7936f-169">Tuttavia, i valori originali non sono disponibili se l'entità è stata disconnessa e quindi collegata in modo esplicito a un altro DbContext, ad esempio con `Attach` o `Update` .</span><span class="sxs-lookup"><span data-stu-id="7936f-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="7936f-170">In questo caso, il valore originale restituito sarà uguale al valore corrente.</span><span class="sxs-lookup"><span data-stu-id="7936f-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="7936f-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aggiornerà solo le proprietà contrassegnate come modificate.</span><span class="sxs-lookup"><span data-stu-id="7936f-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="7936f-172">Impostare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> su true per forzare EF core ad aggiornare un valore di proprietà specificato o impostarlo su false per evitare che EF Core aggiorni il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="7936f-173">[I valori temporanei](xref:core/change-tracking/miscellaneous) vengono in genere generati da EF Core [generatori di valori](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="7936f-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="7936f-174">Impostando il valore corrente di una proprietà, il valore temporaneo viene sostituito con il valore specificato e la proprietà viene contrassegnata come non temporanea.</span><span class="sxs-lookup"><span data-stu-id="7936f-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="7936f-175">Impostare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> su true per forzare un valore come temporaneo anche dopo che è stato impostato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="7936f-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="7936f-176">Utilizzo di una singola navigazione</span><span class="sxs-lookup"><span data-stu-id="7936f-176">Working with a single navigation</span></span>

<span data-ttu-id="7936f-177">Diversi overload di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> consentono l'accesso alle informazioni su una singola esplorazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="7936f-178">Le esplorazioni dei riferimenti a una singola entità correlata sono accessibili tramite i <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> metodi.</span><span class="sxs-lookup"><span data-stu-id="7936f-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="7936f-179">Le esplorazioni di riferimento puntano ai lati "uno" delle relazioni uno-a-molti ed entrambi i lati di una relazione uno-a-uno.</span><span class="sxs-lookup"><span data-stu-id="7936f-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="7936f-180">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="7936f-181">Le esplorazioni possono essere anche raccolte di entità correlate quando vengono usate per i lati "molti" delle relazioni uno-a-molti e molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="7936f-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="7936f-182">I <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> metodi vengono usati per accedere alle spostamenti della raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="7936f-183">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="7936f-184">Alcune operazioni sono comuni per tutte le navigazioni.</span><span class="sxs-lookup"><span data-stu-id="7936f-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="7936f-185">È possibile accedervi sia per le esplorazioni di riferimento che per quelle della raccolta usando il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> metodo.</span><span class="sxs-lookup"><span data-stu-id="7936f-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="7936f-186">Si noti che per accedere a tutte le navigazioni è disponibile solo l'accesso non generico.</span><span class="sxs-lookup"><span data-stu-id="7936f-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="7936f-187">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="7936f-188">Nella tabella seguente sono riepilogate le modalità di utilizzo di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :</span><span class="sxs-lookup"><span data-stu-id="7936f-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="7936f-189">Membro NavigationEntry</span><span class="sxs-lookup"><span data-stu-id="7936f-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="7936f-190">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7936f-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="7936f-191">Ottiene e imposta il valore corrente della navigazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="7936f-192">Si tratta dell'intera raccolta per le esplorazioni della raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="7936f-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadati per la navigazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="7936f-194">Ottiene o imposta un valore che indica se l'entità o la raccolta correlata è stata caricata completamente dal database.</span><span class="sxs-lookup"><span data-stu-id="7936f-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="7936f-195">Carica l'entità o la raccolta correlata dal database. vedere [caricamento esplicito di dati correlati](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="7936f-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="7936f-196">Il EF Core di query utilizzerebbe per caricare questa navigazione come un oggetto `IQueryable` che può essere ulteriormente composto. vedere [caricamento esplicito di dati correlati](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="7936f-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="7936f-197">Uso di tutte le proprietà di un'entità</span><span class="sxs-lookup"><span data-stu-id="7936f-197">Working with all properties of an entity</span></span>

<span data-ttu-id="7936f-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.Generic.IEnumerable%601> di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> per ogni proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="7936f-199">Questa operazione può essere utilizzata per eseguire un'azione per ogni proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="7936f-200">Ad esempio, per impostare qualsiasi proprietà DateTime su `DateTime.Now` :</span><span class="sxs-lookup"><span data-stu-id="7936f-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="7936f-201">Inoltre, EntityEntry contiene diversi metodi per ottenere e impostare contemporaneamente tutti i valori delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="7936f-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="7936f-202">Questi metodi usano la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> classe, che rappresenta una raccolta di proprietà e i relativi valori.</span><span class="sxs-lookup"><span data-stu-id="7936f-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="7936f-203">È possibile ottenere PropertyValue per i valori correnti o originali oppure per i valori attualmente archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="7936f-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="7936f-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="7936f-205">Questi oggetti PropertyValue non sono molto utili.</span><span class="sxs-lookup"><span data-stu-id="7936f-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="7936f-206">Tuttavia, possono essere combinati per eseguire operazioni comuni necessarie per la modifica delle entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="7936f-207">Questa operazione è utile quando si utilizzano oggetti di trasferimento dati e quando si risolvono i [conflitti di concorrenza ottimistica](xref:core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="7936f-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="7936f-208">Le sezioni seguenti illustrano alcuni esempi.</span><span class="sxs-lookup"><span data-stu-id="7936f-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="7936f-209">Impostazione dei valori correnti o originali da un'entità o DTO</span><span class="sxs-lookup"><span data-stu-id="7936f-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="7936f-210">I valori correnti o originali di un'entità possono essere aggiornati copiando i valori da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="7936f-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="7936f-211">Si consideri, ad esempio, un `BlogDto` oggetto di trasferimento dati (dto) con le stesse proprietà del tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="7936f-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="7936f-212">Questa operazione può essere utilizzata per impostare i valori correnti di un'entità rilevata utilizzando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="7936f-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="7936f-213">Questa tecnica viene talvolta utilizzata quando si aggiorna un'entità con valori ottenuti da una chiamata al servizio o da un client in un'applicazione a più livelli.</span><span class="sxs-lookup"><span data-stu-id="7936f-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="7936f-214">Si noti che l'oggetto utilizzato non deve essere dello stesso tipo dell'entità purché disponga di proprietà i cui nomi corrispondono a quelli dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="7936f-215">Nell'esempio precedente viene usata un'istanza di DTO `BlogDto` per impostare i valori correnti di un'entità rilevata `Blog` .</span><span class="sxs-lookup"><span data-stu-id="7936f-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="7936f-216">Si noti che le proprietà verranno contrassegnate come modificate solo se il valore impostato è diverso dal valore corrente.</span><span class="sxs-lookup"><span data-stu-id="7936f-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="7936f-217">Impostazione dei valori correnti o originali da un dizionario</span><span class="sxs-lookup"><span data-stu-id="7936f-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="7936f-218">Nell'esempio precedente i valori sono stati impostati da un'istanza entity o DTO.</span><span class="sxs-lookup"><span data-stu-id="7936f-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="7936f-219">Lo stesso comportamento è disponibile quando i valori delle proprietà vengono archiviati come coppie nome/valore in un dizionario.</span><span class="sxs-lookup"><span data-stu-id="7936f-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="7936f-220">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="7936f-221">Impostazione dei valori correnti o originali dal database</span><span class="sxs-lookup"><span data-stu-id="7936f-221">Setting current or original values from the database</span></span>

<span data-ttu-id="7936f-222">I valori correnti o originali di un'entità possono essere aggiornati con i valori più recenti del database chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> e utilizzando l'oggetto restituito per impostare i valori correnti o originali o entrambi.</span><span class="sxs-lookup"><span data-stu-id="7936f-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="7936f-223">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="7936f-224">Creazione di un oggetto clonato contenente i valori correnti, originali o di database</span><span class="sxs-lookup"><span data-stu-id="7936f-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="7936f-225">L'oggetto PropertyValues restituito da CurrentValues, OriginalValues o GetDatabaseValues può essere usato per creare un clone dell'entità usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7936f-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7936f-226">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="7936f-227">Si noti che `ToObject` restituisce una nuova istanza di che non viene rilevata da DbContext.</span><span class="sxs-lookup"><span data-stu-id="7936f-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="7936f-228">Anche l'oggetto restituito non dispone di relazioni impostate su altre entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="7936f-229">L'oggetto clonato può essere utile per risolvere i problemi relativi agli aggiornamenti simultanei al database, soprattutto quando data binding a oggetti di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="7936f-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="7936f-230">Per ulteriori informazioni, vedere [concorrenza ottimistica](xref:core/saving/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="7936f-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="7936f-231">Uso di tutte le navigazioni di un'entità</span><span class="sxs-lookup"><span data-stu-id="7936f-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="7936f-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.Generic.IEnumerable%601> di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> per ogni navigazione dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="7936f-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> ed <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> eseguono la stessa operazione, ma sono limitati rispettivamente alla navigazione di riferimento o di raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="7936f-234">Questa operazione può essere utilizzata per eseguire un'azione per ogni navigazione dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="7936f-235">Ad esempio, per forzare il caricamento di tutte le entità correlate:</span><span class="sxs-lookup"><span data-stu-id="7936f-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="7936f-236">Utilizzo di tutti i membri di un'entità</span><span class="sxs-lookup"><span data-stu-id="7936f-236">Working with all members of an entity</span></span>

<span data-ttu-id="7936f-237">Le proprietà regolari e le proprietà di navigazione hanno uno stato e un comportamento diversi.</span><span class="sxs-lookup"><span data-stu-id="7936f-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="7936f-238">È quindi normale elaborare le esplorazioni e non le navigazioni separatamente, come illustrato nelle sezioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="7936f-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="7936f-239">Tuttavia, in alcuni casi può essere utile eseguire un'operazione con qualsiasi membro dell'entità, indipendentemente dal fatto che si tratti di una normale proprietà o di una navigazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="7936f-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> sono forniti a questo scopo.</span><span class="sxs-lookup"><span data-stu-id="7936f-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="7936f-241">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="7936f-242">L'esecuzione di questo codice in un Blog dall'esempio genera l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="7936f-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="7936f-243">La [visualizzazione di debug di change tracker Mostra](xref:core/change-tracking/debug-views) informazioni come questa.</span><span class="sxs-lookup"><span data-stu-id="7936f-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="7936f-244">La visualizzazione di debug per l'intero strumento di rilevamento modifiche viene generata dall'utente singolo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> di ogni entità rilevata.</span><span class="sxs-lookup"><span data-stu-id="7936f-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="7936f-245">Find e FindAsync</span><span class="sxs-lookup"><span data-stu-id="7936f-245">Find and FindAsync</span></span>

<span data-ttu-id="7936f-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sono progettati per una ricerca efficiente di una singola entità quando la relativa chiave primaria è nota.</span><span class="sxs-lookup"><span data-stu-id="7936f-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="7936f-247">Trova prima controlla se l'entità è già stata rilevata e, in caso affermativo, restituisce l'entità immediatamente.</span><span class="sxs-lookup"><span data-stu-id="7936f-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="7936f-248">Una query di database viene eseguita solo se l'entità non viene rilevata localmente.</span><span class="sxs-lookup"><span data-stu-id="7936f-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="7936f-249">Si consideri, ad esempio, il codice che chiama find due volte per la stessa entità:</span><span class="sxs-lookup"><span data-stu-id="7936f-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="7936f-250">L'output di questo codice (inclusa la registrazione EF Core) quando si usa SQLite è:</span><span class="sxs-lookup"><span data-stu-id="7936f-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="7936f-251">Si noti che la prima chiamata non trova l'entità localmente e pertanto esegue una query sul database.</span><span class="sxs-lookup"><span data-stu-id="7936f-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="7936f-252">Viceversa, la seconda chiamata restituisce la stessa istanza senza eseguire query sul database perché è già stato rilevato.</span><span class="sxs-lookup"><span data-stu-id="7936f-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="7936f-253">Find restituisce null se un'entità con la chiave specificata non viene rilevata localmente e non esiste nel database.</span><span class="sxs-lookup"><span data-stu-id="7936f-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="7936f-254">Chiavi composte</span><span class="sxs-lookup"><span data-stu-id="7936f-254">Composite keys</span></span>

<span data-ttu-id="7936f-255">Find può essere usato anche con le chiavi composite.</span><span class="sxs-lookup"><span data-stu-id="7936f-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="7936f-256">Si consideri ad esempio un' `OrderLine` entità con una chiave composta costituita dall'ID dell'ordine e dall'ID prodotto:</span><span class="sxs-lookup"><span data-stu-id="7936f-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="7936f-257">La chiave composta deve essere configurata in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> per definire le parti chiave _e il relativo ordine_.</span><span class="sxs-lookup"><span data-stu-id="7936f-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="7936f-258">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="7936f-259">Si noti che `OrderId` è la prima parte della chiave e `ProductId` è la seconda parte della chiave.</span><span class="sxs-lookup"><span data-stu-id="7936f-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="7936f-260">Questo ordine deve essere utilizzato quando si passano i valori di chiave da trovare.</span><span class="sxs-lookup"><span data-stu-id="7936f-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="7936f-261">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="7936f-262">Uso di ChangeTracker. entrys per accedere a tutte le entità rilevate</span><span class="sxs-lookup"><span data-stu-id="7936f-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="7936f-263">Fino a questo momento è stato eseguito l'accesso solo a un singolo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> alla volta.</span><span class="sxs-lookup"><span data-stu-id="7936f-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="7936f-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Restituisce un EntityEntry per ogni entità attualmente rilevata da DbContext.</span><span class="sxs-lookup"><span data-stu-id="7936f-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="7936f-265">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="7936f-266">Questo codice genera l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="7936f-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="7936f-267">Si noti che vengono restituite le voci relative ai Blog e ai post.</span><span class="sxs-lookup"><span data-stu-id="7936f-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="7936f-268">I risultati possono invece essere filtrati in base a un tipo di entità specifico usando l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> Overload Generico:</span><span class="sxs-lookup"><span data-stu-id="7936f-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="7936f-269">L'output di questo codice mostra che vengono restituiti solo i post:</span><span class="sxs-lookup"><span data-stu-id="7936f-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="7936f-270">Inoltre, l'utilizzo dell'overload generico restituisce <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> istanze generiche.</span><span class="sxs-lookup"><span data-stu-id="7936f-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="7936f-271">Questo è ciò che consente l'accesso di tipo Fluent alla `Id` Proprietà in questo esempio.</span><span class="sxs-lookup"><span data-stu-id="7936f-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="7936f-272">Il tipo generico usato per il filtro non deve essere un tipo di entità mappato; è invece possibile usare un'interfaccia o un tipo di base non mappato.</span><span class="sxs-lookup"><span data-stu-id="7936f-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="7936f-273">Ad esempio, se tutti i tipi di entità nel modello implementano un'interfaccia che definisce la relativa proprietà chiave:</span><span class="sxs-lookup"><span data-stu-id="7936f-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="7936f-274">Questa interfaccia può quindi essere usata per usare la chiave di qualsiasi entità rilevata in modo fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="7936f-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="7936f-275">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="7936f-276">Uso di DbSet. local per eseguire query sulle entità rilevate</span><span class="sxs-lookup"><span data-stu-id="7936f-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="7936f-277">EF Core query vengono sempre eseguite nel database e restituiscono solo le entità che sono state salvate nel database.</span><span class="sxs-lookup"><span data-stu-id="7936f-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="7936f-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> fornisce un meccanismo per eseguire una query su DbContext per le entità rilevate locali.</span><span class="sxs-lookup"><span data-stu-id="7936f-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="7936f-279">Poiché `DbSet.Local` viene usato per eseguire query sulle entità rilevate, è tipico caricare le entità in DbContext e quindi usare le entità caricate.</span><span class="sxs-lookup"><span data-stu-id="7936f-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="7936f-280">Questo vale soprattutto per data binding, ma può essere utile anche in altre situazioni.</span><span class="sxs-lookup"><span data-stu-id="7936f-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="7936f-281">Nel codice seguente, ad esempio, viene innanzitutto eseguita una query sul database per tutti i Blog e i post.</span><span class="sxs-lookup"><span data-stu-id="7936f-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="7936f-282">Il <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> metodo di estensione viene utilizzato per eseguire la query con i risultati rilevati dal contesto senza essere restituiti direttamente all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="7936f-283">( `ToList` L'uso di o simile ha lo stesso effetto ma con l'overhead della creazione dell'elenco restituito, che non è necessario in questo caso). Nell'esempio viene quindi usato `DbSet.Local` per accedere alle entità rilevate localmente:</span><span class="sxs-lookup"><span data-stu-id="7936f-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="7936f-284">Si noti che, a differenza <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> di, `DbSet.Local` restituisce direttamente le istanze dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="7936f-285">Un EntityEntry può, ovviamente, essere ottenuto sempre per l'entità restituita chiamando <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7936f-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="7936f-286">Visualizzazione locale</span><span class="sxs-lookup"><span data-stu-id="7936f-286">The local view</span></span>

<span data-ttu-id="7936f-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Restituisce una visualizzazione delle entità rilevate localmente che riflette la corrente <xref:Microsoft.EntityFrameworkCore.EntityState> di tali entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="7936f-288">In particolare, ciò significa che:</span><span class="sxs-lookup"><span data-stu-id="7936f-288">Specifically, this means that:</span></span>

- <span data-ttu-id="7936f-289">`Added` sono incluse le entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-289">`Added` entities are included.</span></span> <span data-ttu-id="7936f-290">Si noti che questo non avviene per le normali query di EF Core, poiché `Added` le entità non esistono ancora nel database e pertanto non vengono mai restituite da una query di database.</span><span class="sxs-lookup"><span data-stu-id="7936f-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="7936f-291">`Deleted` le entità sono escluse.</span><span class="sxs-lookup"><span data-stu-id="7936f-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="7936f-292">Si noti che questo non è il caso per le normali query di EF Core, poiché `Deleted` le entità _sono_ ancora presenti nel database e pertanto vengono restituite dalle query di database.</span><span class="sxs-lookup"><span data-stu-id="7936f-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="7936f-293">Ciò significa che viene visualizzato `DbSet.Local` sui dati che riflettono lo stato concettuale corrente dell'entità Graph, con le `Added` entità incluse ed `Deleted` entità escluse.</span><span class="sxs-lookup"><span data-stu-id="7936f-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="7936f-294">Questo corrisponde allo stato del database previsto dopo la chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="7936f-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="7936f-295">Si tratta in genere della visualizzazione ideale per data binding, perché presenta all'utente i dati che lo conoscono in base alle modifiche apportate dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7936f-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="7936f-296">Il codice seguente illustra questa operazione contrassegnando un post come `Deleted` e quindi aggiungendo un nuovo post, contrassegnando come `Added` :</span><span class="sxs-lookup"><span data-stu-id="7936f-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="7936f-297">L'output di questo codice è:</span><span class="sxs-lookup"><span data-stu-id="7936f-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="7936f-298">Si noti che il post eliminato viene rimosso dalla visualizzazione locale e il post aggiunto è incluso.</span><span class="sxs-lookup"><span data-stu-id="7936f-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="7936f-299">Uso di local per aggiungere e rimuovere entità</span><span class="sxs-lookup"><span data-stu-id="7936f-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="7936f-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> restituisce un'istanza di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span><span class="sxs-lookup"><span data-stu-id="7936f-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="7936f-301">Si tratta di un'implementazione di <xref:System.Collections.Generic.ICollection%601> che genera e risponde alle notifiche quando le entità vengono aggiunte e rimosse dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="7936f-302">Questo è lo stesso concetto di <xref:System.Collections.ObjectModel.ObservableCollection%601> , ma implementato come una proiezione su voci di rilevamento delle modifiche EF core esistenti, anziché come una raccolta indipendente.</span><span class="sxs-lookup"><span data-stu-id="7936f-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="7936f-303">Le notifiche della visualizzazione locale sono collegate al rilevamento delle modifiche DbContext in modo che la visualizzazione locale rimanga sincronizzata con il DbContext.</span><span class="sxs-lookup"><span data-stu-id="7936f-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="7936f-304">In particolare:</span><span class="sxs-lookup"><span data-stu-id="7936f-304">Specifically:</span></span>

- <span data-ttu-id="7936f-305">L'aggiunta di una nuova entità a `DbSet.Local` ne determina il rilevamento da parte di DbContext, in genere nello `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="7936f-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="7936f-306">Se l'entità ha già un valore di chiave generato, viene rilevata come in `Unchanged` alternativa.</span><span class="sxs-lookup"><span data-stu-id="7936f-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="7936f-307">La rimozione di un'entità da `DbSet.Local` ne determina la contrassegno come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="7936f-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="7936f-308">Un'entità che viene rilevata da DbContext verrà automaticamente visualizzata nella `DbSet.Local` raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="7936f-309">Se, ad esempio, si esegue una query per importare automaticamente più entità, la visualizzazione locale verrà aggiornata.</span><span class="sxs-lookup"><span data-stu-id="7936f-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="7936f-310">Un'entità contrassegnata come `Deleted` verrà rimossa automaticamente dalla raccolta locale.</span><span class="sxs-lookup"><span data-stu-id="7936f-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="7936f-311">Ciò significa che la visualizzazione locale può essere usata per modificare le entità rilevate semplicemente aggiungendo e rimuovendo dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="7936f-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="7936f-312">Ad esempio, è possibile modificare il codice di esempio precedente per aggiungere e rimuovere post dalla raccolta locale:</span><span class="sxs-lookup"><span data-stu-id="7936f-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="7936f-313">L'output rimane invariato rispetto all'esempio precedente, perché le modifiche apportate alla visualizzazione locale vengono sincronizzate con DbContext.</span><span class="sxs-lookup"><span data-stu-id="7936f-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="7936f-314">Uso della visualizzazione locale per Windows Forms o WPF data binding</span><span class="sxs-lookup"><span data-stu-id="7936f-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="7936f-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> costituisce la base per data binding per EF Core entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="7936f-316">Tuttavia, sia Windows Forms che WPF funzionano meglio se usati con il tipo specifico di raccolta di notifica che prevedono.</span><span class="sxs-lookup"><span data-stu-id="7936f-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="7936f-317">La visualizzazione locale supporta la creazione di questi tipi di raccolta specifici:</span><span class="sxs-lookup"><span data-stu-id="7936f-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="7936f-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.ObjectModel.ObservableCollection%601> per Data Binding WPF.</span><span class="sxs-lookup"><span data-stu-id="7936f-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="7936f-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Restituisce un oggetto <xref:System.ComponentModel.BindingList%601> per Windows Forms Data Binding.</span><span class="sxs-lookup"><span data-stu-id="7936f-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="7936f-320">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7936f-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="7936f-321">Per ulteriori informazioni sui data binding WPF con EF Core, vedere [Introduzione a WPF](xref:core/get-started/wpf) .</span><span class="sxs-lookup"><span data-stu-id="7936f-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="7936f-322">La visualizzazione locale per un'istanza di DbSet specificata viene creata in modo differito quando si accede per la prima volta e quindi nella cache.</span><span class="sxs-lookup"><span data-stu-id="7936f-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="7936f-323">La creazione di LocalView è rapida e non usa memoria significativa.</span><span class="sxs-lookup"><span data-stu-id="7936f-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="7936f-324">Tuttavia, chiama [DetectChanges](xref:core/change-tracking/change-detection), che può essere lento per un numero elevato di entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="7936f-325">Le raccolte create da `ToObservableCollection` e `ToBindingList` vengono create anche in modalità differita e quindi memorizzate nella cache.</span><span class="sxs-lookup"><span data-stu-id="7936f-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="7936f-326">Entrambi questi metodi creano nuove raccolte, che possono essere lente e utilizzano una grande quantità di memoria quando sono implicate migliaia di entità.</span><span class="sxs-lookup"><span data-stu-id="7936f-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
