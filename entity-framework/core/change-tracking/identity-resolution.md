---
title: Risoluzione di identità-EF Core
description: Risoluzione di più istanze di entità in una singola istanza con valori di chiave primaria
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: f94b61371dcead27853799719dabc7849500d466
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129770"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="d6599-103">Risoluzione di identità in EF Core</span><span class="sxs-lookup"><span data-stu-id="d6599-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="d6599-104">Un oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> può rilevare solo un'istanza di entità con qualsiasi valore di chiave primaria specificato.</span><span class="sxs-lookup"><span data-stu-id="d6599-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="d6599-105">Ciò significa che più istanze di un'entità con lo stesso valore di chiave devono essere risolte in una singola istanza.</span><span class="sxs-lookup"><span data-stu-id="d6599-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="d6599-106">Questa operazione è denominata "risoluzione identità".</span><span class="sxs-lookup"><span data-stu-id="d6599-106">This is called "identity resolution".</span></span> <span data-ttu-id="d6599-107">La risoluzione di identità garantisce che Entity Framework Core (EF Core) stia monitorando un grafo coerente senza ambiguità sulle relazioni o sui valori delle proprietà delle entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="d6599-108">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="d6599-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="d6599-109">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="d6599-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="d6599-110">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span><span class="sxs-lookup"><span data-stu-id="d6599-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="d6599-111">Introduzione</span><span class="sxs-lookup"><span data-stu-id="d6599-111">Introduction</span></span>

<span data-ttu-id="d6599-112">Il codice seguente esegue una query per un'entità e quindi tenta di associare un'istanza diversa con lo stesso valore di chiave primaria:</span><span class="sxs-lookup"><span data-stu-id="d6599-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="d6599-113">L'esecuzione di questo codice comporta l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="d6599-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="d6599-114">System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità' Blog ' perché è già stata rilevata un'altra istanza con il valore di chiave ' {ID: 1}'.</span><span class="sxs-lookup"><span data-stu-id="d6599-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="d6599-115">Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.</span><span class="sxs-lookup"><span data-stu-id="d6599-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="d6599-116">EF Core richiede una sola istanza perché:</span><span class="sxs-lookup"><span data-stu-id="d6599-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="d6599-117">I valori delle proprietà possono essere diversi tra più istanze.</span><span class="sxs-lookup"><span data-stu-id="d6599-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="d6599-118">Quando si aggiorna il database, EF Core necessario stabilire quali valori di proprietà utilizzare.</span><span class="sxs-lookup"><span data-stu-id="d6599-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="d6599-119">Le relazioni con altre entità possono essere diverse tra più istanze.</span><span class="sxs-lookup"><span data-stu-id="d6599-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="d6599-120">Ad esempio, "bloga" può essere correlato a una raccolta di post diversa da "blogB".</span><span class="sxs-lookup"><span data-stu-id="d6599-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="d6599-121">L'eccezione precedente viene comunemente rilevata nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d6599-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="d6599-122">Quando si tenta di aggiornare un'entità</span><span class="sxs-lookup"><span data-stu-id="d6599-122">When attempting to update an entity</span></span>
- <span data-ttu-id="d6599-123">Quando si tenta di tenere traccia di un grafico serializzato di entità</span><span class="sxs-lookup"><span data-stu-id="d6599-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="d6599-124">Quando non si riesce a impostare un valore di chiave che non viene generato automaticamente</span><span class="sxs-lookup"><span data-stu-id="d6599-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="d6599-125">Quando si riutilizza un'istanza di DbContext per più unità di lavoro</span><span class="sxs-lookup"><span data-stu-id="d6599-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="d6599-126">Ognuna di queste situazioni viene illustrata nelle sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="d6599-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="d6599-127">Aggiornamento di un'entità</span><span class="sxs-lookup"><span data-stu-id="d6599-127">Updating an entity</span></span>

<span data-ttu-id="d6599-128">Sono disponibili diversi approcci per aggiornare un'entità con nuovi valori, come analizzati in [rilevamento modifiche in EF Core](xref:core/change-tracking/index) e [Rilevamento esplicito delle entità](xref:core/change-tracking/explicit-tracking).</span><span class="sxs-lookup"><span data-stu-id="d6599-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="d6599-129">Questi approcci sono descritti di seguito nel contesto della risoluzione delle identità.</span><span class="sxs-lookup"><span data-stu-id="d6599-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="d6599-130">Un punto importante da notare è che ognuno degli approcci utilizza una query o una chiamata a uno di `Update` o `Attach` , ma **_mai entrambi_**.</span><span class="sxs-lookup"><span data-stu-id="d6599-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="d6599-131">Chiama aggiornamento</span><span class="sxs-lookup"><span data-stu-id="d6599-131">Call Update</span></span>

<span data-ttu-id="d6599-132">Spesso l'entità da aggiornare non proviene da una query in DbContext che verrà usata per SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="d6599-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="d6599-133">In un'applicazione Web, ad esempio, è possibile creare un'istanza dell'entità dalle informazioni contenute in una richiesta POST.</span><span class="sxs-lookup"><span data-stu-id="d6599-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="d6599-134">Il modo più semplice per gestire questo problema consiste nell'usare <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d6599-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d6599-135">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="d6599-136">In questo caso:</span><span class="sxs-lookup"><span data-stu-id="d6599-136">In this case:</span></span>

- <span data-ttu-id="d6599-137">Viene creata solo una singola istanza dell'entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="d6599-138">L'istanza dell'entità **non** viene sottoposta a query dal database durante l'esecuzione dell'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="d6599-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="d6599-139">Tutti i valori delle proprietà verranno aggiornati nel database, indipendentemente dal fatto che siano stati effettivamente modificati o meno.</span><span class="sxs-lookup"><span data-stu-id="d6599-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="d6599-140">Viene eseguito un round trip del database.</span><span class="sxs-lookup"><span data-stu-id="d6599-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="d6599-141">Query che applicano le modifiche</span><span class="sxs-lookup"><span data-stu-id="d6599-141">Query then apply changes</span></span>

<span data-ttu-id="d6599-142">In genere non è noto quali valori di proprietà sono stati effettivamente modificati quando un'entità viene creata dalle informazioni in una richiesta POST o simile.</span><span class="sxs-lookup"><span data-stu-id="d6599-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="d6599-143">Spesso è sufficiente aggiornare tutti i valori nel database, come nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="d6599-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="d6599-144">Tuttavia, se l'applicazione gestisce molte entità e solo un numero ridotto di tali entità ha modifiche effettive, potrebbe essere utile limitare gli aggiornamenti inviati.</span><span class="sxs-lookup"><span data-stu-id="d6599-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="d6599-145">Questa operazione può essere eseguita eseguendo una query per tenere traccia delle entità al momento esistenti nel database e quindi applicando le modifiche alle entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="d6599-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="d6599-146">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="d6599-147">In questo caso:</span><span class="sxs-lookup"><span data-stu-id="d6599-147">In this case:</span></span>

- <span data-ttu-id="d6599-148">Viene tenuta traccia solo di una singola istanza dell'entità. quello restituito dal database dalla `Find` query.</span><span class="sxs-lookup"><span data-stu-id="d6599-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="d6599-149">`Update`, `Attach` e così via **non** vengono usati.</span><span class="sxs-lookup"><span data-stu-id="d6599-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="d6599-150">Solo i valori delle proprietà effettivamente modificati vengono aggiornati nel database.</span><span class="sxs-lookup"><span data-stu-id="d6599-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="d6599-151">Vengono eseguiti due round trip del database.</span><span class="sxs-lookup"><span data-stu-id="d6599-151">Two database round-trips are made.</span></span>

<span data-ttu-id="d6599-152">EF Core dispone di alcuni helper per il trasferimento dei valori delle proprietà come questo.</span><span class="sxs-lookup"><span data-stu-id="d6599-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="d6599-153">Ad esempio, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copia tutti i valori dall'oggetto specificato e li imposta nell'oggetto rilevato:</span><span class="sxs-lookup"><span data-stu-id="d6599-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="d6599-154">`SetValues` accetta vari tipi di oggetto, inclusi DTO (Data Transfer Objects) con nomi di proprietà che corrispondono alle proprietà del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="d6599-155">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="d6599-156">O un dizionario con voci nome/valore per i valori delle proprietà:</span><span class="sxs-lookup"><span data-stu-id="d6599-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="d6599-157">Per ulteriori informazioni sull'utilizzo di valori di proprietà come questo, vedere [accesso alle entità registrate](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="d6599-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="d6599-158">Usa valori originali</span><span class="sxs-lookup"><span data-stu-id="d6599-158">Use original values</span></span>

<span data-ttu-id="d6599-159">Fino a questo punto, ogni approccio ha eseguito una query prima di eseguire l'aggiornamento o ha aggiornato tutti i valori delle proprietà indipendentemente dal fatto che siano stati modificati o meno.</span><span class="sxs-lookup"><span data-stu-id="d6599-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="d6599-160">Per aggiornare solo i valori che sono stati modificati senza eseguire query come parte dell'aggiornamento, sono necessarie informazioni specifiche sui valori della proprietà modificati.</span><span class="sxs-lookup"><span data-stu-id="d6599-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="d6599-161">Un modo comune per ottenere queste informazioni consiste nell'inviare di nuovo i valori correnti e originali in HTTP post o simili.</span><span class="sxs-lookup"><span data-stu-id="d6599-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="d6599-162">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="d6599-163">In questo codice l'entità con i valori modificati viene inizialmente collegata.</span><span class="sxs-lookup"><span data-stu-id="d6599-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="d6599-164">Questo fa in modo che EF Core tenga traccia dell'entità nello `Unchanged` stato, ovvero senza alcun valore di proprietà contrassegnato come modificato.</span><span class="sxs-lookup"><span data-stu-id="d6599-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="d6599-165">Il dizionario dei valori originali viene quindi applicato a questa entità rilevata.</span><span class="sxs-lookup"><span data-stu-id="d6599-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="d6599-166">Si contrassegna come proprietà modificate con valori correnti e originali diversi.</span><span class="sxs-lookup"><span data-stu-id="d6599-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="d6599-167">Le proprietà con gli stessi valori correnti e originali non verranno contrassegnate come modificate.</span><span class="sxs-lookup"><span data-stu-id="d6599-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="d6599-168">In questo caso:</span><span class="sxs-lookup"><span data-stu-id="d6599-168">In this case:</span></span>

- <span data-ttu-id="d6599-169">Viene rilevata solo una singola istanza dell'entità utilizzando Connetti.</span><span class="sxs-lookup"><span data-stu-id="d6599-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="d6599-170">L'istanza dell'entità **non** viene sottoposta a query dal database durante l'esecuzione dell'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="d6599-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="d6599-171">L'applicazione dei valori originali garantisce che solo i valori delle proprietà effettivamente modificati vengano aggiornati nel database.</span><span class="sxs-lookup"><span data-stu-id="d6599-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="d6599-172">Viene eseguito un round trip del database.</span><span class="sxs-lookup"><span data-stu-id="d6599-172">One database round-trip is made.</span></span>

<span data-ttu-id="d6599-173">Come per gli esempi nella sezione precedente, i valori originali non devono essere passati come dizionario. funzionerà anche un'istanza di entità o un DTO.</span><span class="sxs-lookup"><span data-stu-id="d6599-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="d6599-174">Sebbene questo approccio abbia caratteristiche accattivanti, richiede l'invio dei valori originali dell'entità da e verso il client Web.</span><span class="sxs-lookup"><span data-stu-id="d6599-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="d6599-175">Valutare attentamente se questa complessità aggiuntiva è utile per i vantaggi. per molte applicazioni uno degli approcci più semplici è più pragmatico.</span><span class="sxs-lookup"><span data-stu-id="d6599-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="d6599-176">Associazione di un grafo serializzato</span><span class="sxs-lookup"><span data-stu-id="d6599-176">Attaching a serialized graph</span></span>

<span data-ttu-id="d6599-177">EF Core funziona con grafici di entità connesse tramite chiavi esterne e proprietà di navigazione, come descritto in [modifica di chiavi esterne e spostamenti](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="d6599-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="d6599-178">Se questi grafici vengono creati all'esterno di EF Core usando, ad esempio, da un file JSON, possono avere più istanze della stessa entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="d6599-179">Questi duplicati devono essere risolti in istanze singole prima che il grafico possa essere rilevato.</span><span class="sxs-lookup"><span data-stu-id="d6599-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="d6599-180">Grafici senza duplicati</span><span class="sxs-lookup"><span data-stu-id="d6599-180">Graphs with no duplicates</span></span>

<span data-ttu-id="d6599-181">Prima di continuare, è importante riconoscere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="d6599-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="d6599-182">I serializzatori spesso dispongono di opzioni per la gestione di cicli e istanze duplicate nel grafo.</span><span class="sxs-lookup"><span data-stu-id="d6599-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="d6599-183">La scelta dell'oggetto usato come radice del grafico può spesso contribuire alla riduzione o alla rimozione di duplicati.</span><span class="sxs-lookup"><span data-stu-id="d6599-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="d6599-184">Se possibile, utilizzare le opzioni di serializzazione e scegliere le radici che non comportano duplicati.</span><span class="sxs-lookup"><span data-stu-id="d6599-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="d6599-185">Ad esempio, il codice seguente usa [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/) per serializzare un elenco di Blog ciascuno con i post associati:</span><span class="sxs-lookup"><span data-stu-id="d6599-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="d6599-186">Il codice JSON generato da questo codice è:</span><span class="sxs-lookup"><span data-stu-id="d6599-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="d6599-187">Si noti che nel codice JSON non sono presenti Blog o post duplicati.</span><span class="sxs-lookup"><span data-stu-id="d6599-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="d6599-188">Ciò significa che le chiamate semplici a `Update` funzioneranno per aggiornare queste entità nel database:</span><span class="sxs-lookup"><span data-stu-id="d6599-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="d6599-189">Gestione dei duplicati</span><span class="sxs-lookup"><span data-stu-id="d6599-189">Handling duplicates</span></span>

<span data-ttu-id="d6599-190">Il codice dell'esempio precedente ha serializzato ogni blog con i relativi post associati.</span><span class="sxs-lookup"><span data-stu-id="d6599-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="d6599-191">Se questo viene modificato per serializzare ogni post con il Blog associato, i duplicati vengono introdotti nel JSON serializzato.</span><span class="sxs-lookup"><span data-stu-id="d6599-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="d6599-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="d6599-193">Il codice JSON serializzato è ora simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="d6599-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="d6599-194">Si noti che il grafico include ora più istanze di Blog con lo stesso valore di chiave, oltre a più istanze post con lo stesso valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="d6599-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="d6599-195">Il tentativo di tenere traccia di questo grafico come nell'esempio precedente genererà:</span><span class="sxs-lookup"><span data-stu-id="d6599-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="d6599-196">System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità "post" perché è già stata rilevata un'altra istanza con il valore di chiave "{ID: 2}".</span><span class="sxs-lookup"><span data-stu-id="d6599-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="d6599-197">Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.</span><span class="sxs-lookup"><span data-stu-id="d6599-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="d6599-198">È possibile risolvere il problema in due modi:</span><span class="sxs-lookup"><span data-stu-id="d6599-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="d6599-199">Usare le opzioni di serializzazione JSON che conservano i riferimenti</span><span class="sxs-lookup"><span data-stu-id="d6599-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="d6599-200">Eseguire la risoluzione dell'identità mentre è in corso il rilevamento del grafico</span><span class="sxs-lookup"><span data-stu-id="d6599-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="d6599-201">Mantenere i riferimenti</span><span class="sxs-lookup"><span data-stu-id="d6599-201">Preserve references</span></span>

<span data-ttu-id="d6599-202">Json.NET offre la `PreserveReferencesHandling` possibilità di gestire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="d6599-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="d6599-203">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="d6599-204">Il codice JSON risultante è ora simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="d6599-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="d6599-205">Si noti che questo JSON ha sostituito i duplicati con riferimenti come `"$ref": "5"` che fanno riferimento all'istanza già esistente nel grafico.</span><span class="sxs-lookup"><span data-stu-id="d6599-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="d6599-206">È possibile tenere traccia di questo grafico utilizzando le semplici chiamate a `Update` , come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="d6599-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="d6599-207">Il <xref:System.Text.Json> supporto nelle librerie di classi di base .NET (BCL) ha un'opzione simile che produce lo stesso risultato.</span><span class="sxs-lookup"><span data-stu-id="d6599-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="d6599-208">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="d6599-209">Risolvere i duplicati</span><span class="sxs-lookup"><span data-stu-id="d6599-209">Resolve duplicates</span></span>

<span data-ttu-id="d6599-210">Se non è possibile eliminare i duplicati nel processo di serializzazione, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fornisce un modo per gestire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="d6599-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="d6599-211">TrackGraph funziona come `Add` `Attach` e, `Update` ad eccezione del fatto che genera un callback per ogni istanza di entità prima di tenerne traccia.</span><span class="sxs-lookup"><span data-stu-id="d6599-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="d6599-212">Questo callback può essere usato per tenere traccia dell'entità o ignorarla.</span><span class="sxs-lookup"><span data-stu-id="d6599-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="d6599-213">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="d6599-214">Per ogni entità nel grafico, il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d6599-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="d6599-215">Trovare il tipo di entità e il valore della chiave dell'entità</span><span class="sxs-lookup"><span data-stu-id="d6599-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="d6599-216">Ricerca dell'entità con questa chiave nello strumento di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="d6599-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="d6599-217">Se l'entità viene trovata, non viene eseguita alcuna azione aggiuntiva perché l'entità è un duplicato</span><span class="sxs-lookup"><span data-stu-id="d6599-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="d6599-218">Se l'entità non viene trovata, viene rilevata impostando lo stato su `Modified`</span><span class="sxs-lookup"><span data-stu-id="d6599-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="d6599-219">L'output dell'esecuzione del codice è:</span><span class="sxs-lookup"><span data-stu-id="d6599-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="d6599-220">Questo codice **presuppone che tutti i duplicati siano identici**.</span><span class="sxs-lookup"><span data-stu-id="d6599-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="d6599-221">In questo modo è possibile scegliere arbitrariamente uno dei duplicati da rilevare durante l'eliminazione degli altri.</span><span class="sxs-lookup"><span data-stu-id="d6599-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="d6599-222">Se i duplicati possono essere diversi, il codice dovrà decidere come determinare quale usare e come combinare insieme i valori di proprietà e di navigazione.</span><span class="sxs-lookup"><span data-stu-id="d6599-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="d6599-223">Per semplicità, questo codice presuppone che ogni entità disponga di una proprietà di chiave primaria denominata `Id` .</span><span class="sxs-lookup"><span data-stu-id="d6599-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="d6599-224">Questo può essere codificato in una classe di base astratta o in un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="d6599-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="d6599-225">In alternativa, è possibile ottenere la proprietà o le proprietà della chiave primaria dai <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati in modo che questo codice funzioni con qualsiasi tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="d6599-226">Errore di impostazione dei valori di chiave</span><span class="sxs-lookup"><span data-stu-id="d6599-226">Failing to set key values</span></span>

<span data-ttu-id="d6599-227">I tipi di entità sono spesso configurati per l'utilizzo di [valori di chiave generati automaticamente](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="d6599-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="d6599-228">Si tratta dell'impostazione predefinita per le proprietà Integer e GUID di chiavi non composte.</span><span class="sxs-lookup"><span data-stu-id="d6599-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="d6599-229">Tuttavia, se il tipo di entità non è configurato per l'utilizzo di valori di chiave generati automaticamente, è necessario impostare un valore di chiave esplicito prima di tenere traccia dell'entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="d6599-230">Ad esempio, usando il tipo di entità seguente:</span><span class="sxs-lookup"><span data-stu-id="d6599-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="d6599-231">Si consideri il codice che tenta di rilevare due nuove istanze di entità senza impostare i valori delle chiavi:</span><span class="sxs-lookup"><span data-stu-id="d6599-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="d6599-232">Questo codice genererà:</span><span class="sxs-lookup"><span data-stu-id="d6599-232">This code will throw:</span></span>

> <span data-ttu-id="d6599-233">System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità' Pet ' perché è già stata rilevata un'altra istanza con il valore di chiave ' {ID: 0}'.</span><span class="sxs-lookup"><span data-stu-id="d6599-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="d6599-234">Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.</span><span class="sxs-lookup"><span data-stu-id="d6599-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="d6599-235">Per risolvere il problema, impostare i valori di chiave in modo esplicito o configurare la proprietà chiave per l'utilizzo di valori di chiave generati.</span><span class="sxs-lookup"><span data-stu-id="d6599-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="d6599-236">Per ulteriori informazioni, vedere [valori generati](xref:core/modeling/generated-properties) .</span><span class="sxs-lookup"><span data-stu-id="d6599-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="d6599-237">Sovrautilizzo di una singola istanza di DbContext</span><span class="sxs-lookup"><span data-stu-id="d6599-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="d6599-238"><xref:Microsoft.EntityFrameworkCore.DbContext> è progettato per rappresentare un'unità di lavoro di breve durata, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index), ed è stato elaborato in [rilevamento modifiche in EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="d6599-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="d6599-239">Non seguendo queste linee guida è facile eseguire situazioni in cui viene effettuato un tentativo di tenere traccia di più istanze della stessa entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="d6599-240">Esempi comuni:</span><span class="sxs-lookup"><span data-stu-id="d6599-240">Common examples are:</span></span>

- <span data-ttu-id="d6599-241">Utilizzando la stessa istanza di DbContext per impostare lo stato di test ed eseguire il test.</span><span class="sxs-lookup"><span data-stu-id="d6599-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="d6599-242">Ciò comporta spesso l'DbContext ancora il rilevamento di un'istanza dell'entità dalla configurazione del test, tentando quindi di alleghiare una nuova istanza nel test.</span><span class="sxs-lookup"><span data-stu-id="d6599-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="d6599-243">Usare invece un'istanza di DbContext diversa per impostare lo stato di test e il codice di test appropriato.</span><span class="sxs-lookup"><span data-stu-id="d6599-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="d6599-244">Uso di un'istanza di DbContext condivisa in un repository o codice simile.</span><span class="sxs-lookup"><span data-stu-id="d6599-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="d6599-245">Assicurarsi invece che il repository usi una singola istanza di DbContext per ogni unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="d6599-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="d6599-246">Risoluzione di identità e query</span><span class="sxs-lookup"><span data-stu-id="d6599-246">Identity resolution and queries</span></span>

<span data-ttu-id="d6599-247">La risoluzione delle identità viene eseguita automaticamente quando le entità vengono rilevate da una query.</span><span class="sxs-lookup"><span data-stu-id="d6599-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="d6599-248">Ciò significa che se un'istanza di entità con un valore di chiave specificato è già rilevata, viene utilizzata l'istanza rilevata esistente anziché creare una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="d6599-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="d6599-249">Questa operazione ha una conseguenza importante: se i dati sono stati modificati nel database, questo non verrà riflesso nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="d6599-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="d6599-250">Questo è un buon motivo per usare una nuova istanza di DbContext per ogni unità di lavoro, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index), ed è stata elaborata in [rilevamento modifiche in EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="d6599-250">This is angood reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6599-251">È importante comprendere che EF Core esegue sempre una query LINQ su un DbSet sul database e restituisce solo i risultati in base a ciò che è presente nel database.</span><span class="sxs-lookup"><span data-stu-id="d6599-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="d6599-252">Tuttavia, per una query di rilevamento, se le entità restituite sono già rilevate, vengono utilizzate le istanze rilevate anziché creare istanze dai dati nel database.</span><span class="sxs-lookup"><span data-stu-id="d6599-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="d6599-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> in alternativa <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> , è possibile usare se le entità rilevate devono essere aggiornate con i dati più recenti del database.</span><span class="sxs-lookup"><span data-stu-id="d6599-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="d6599-254">Per ulteriori informazioni, vedere [accesso alle entità registrate](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="d6599-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="d6599-255">A differenza delle query di rilevamento, le query senza rilevamento non eseguono la risoluzione delle identità.</span><span class="sxs-lookup"><span data-stu-id="d6599-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="d6599-256">Ciò significa che le query senza rilevamento possono restituire duplicati proprio come nel caso della serializzazione JSON descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="d6599-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="d6599-257">Questo non è in genere un problema se i risultati della query verranno serializzati e inviati al client.</span><span class="sxs-lookup"><span data-stu-id="d6599-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="d6599-258">Non eseguire periodicamente una query senza rilevamento e quindi alleghi le entità restituite allo stesso contesto.</span><span class="sxs-lookup"><span data-stu-id="d6599-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="d6599-259">Questa operazione sarà più lenta e più difficile da ottenere rispetto all'utilizzo di una query di rilevamento.</span><span class="sxs-lookup"><span data-stu-id="d6599-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="d6599-260">Le query senza rilevamento non eseguono la risoluzione delle identità perché questa operazione influisca sulle prestazioni di streaming di un numero elevato di entità da una query.</span><span class="sxs-lookup"><span data-stu-id="d6599-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="d6599-261">Questo è dovuto al fatto che la risoluzione delle identità richiede il rilevamento di ogni istanza restituita in modo che possa essere utilizzata anziché creare un duplicato in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="d6599-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="d6599-262">A partire da EF Core 5,0, è possibile forzare l'esecuzione di query senza rilevamento per eseguire la risoluzione dell'identità tramite <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> .</span><span class="sxs-lookup"><span data-stu-id="d6599-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="d6599-263">La query terrà quindi traccia delle istanze restituite (senza verificarle in modo normale) e assicurarsi che non vengano creati duplicati nei risultati della query.</span><span class="sxs-lookup"><span data-stu-id="d6599-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="d6599-264">Override dell'uguaglianza di oggetti</span><span class="sxs-lookup"><span data-stu-id="d6599-264">Overriding object equality</span></span>

<span data-ttu-id="d6599-265">EF Core usa l' [uguaglianza dei riferimenti](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) durante il confronto di istanze di entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="d6599-266">Questa situazione si verifica anche se i tipi di entità sostituiscono <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> o modificano in altro modo l'uguaglianza degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="d6599-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="d6599-267">Tuttavia, esiste un'unica posizione in cui l'uguaglianza di override può influisca sul comportamento EF Core: quando le esplorazioni della raccolta usano l'uguaglianza sottoposta a override anziché l'uguaglianza dei riferimenti, e quindi segnalano più istanze come uguali.</span><span class="sxs-lookup"><span data-stu-id="d6599-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="d6599-268">Per questo motivo è consigliabile evitare di eseguire l'override dell'uguaglianza delle entità.</span><span class="sxs-lookup"><span data-stu-id="d6599-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="d6599-269">Se viene usato, assicurarsi di creare le esplorazioni della raccolta che forzano l'uguaglianza di riferimenti.</span><span class="sxs-lookup"><span data-stu-id="d6599-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="d6599-270">Ad esempio, creare un operatore di confronto di uguaglianza che usa l'uguaglianza dei riferimenti:</span><span class="sxs-lookup"><span data-stu-id="d6599-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="d6599-271">(A partire da .NET 5, questo è incluso in BCL come <xref:System.Collections.Generic.ReferenceEqualityComparer> ).</span><span class="sxs-lookup"><span data-stu-id="d6599-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="d6599-272">Questo operatore di confronto può quindi essere utilizzato durante la creazione di spostamenti della raccolta.</span><span class="sxs-lookup"><span data-stu-id="d6599-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="d6599-273">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d6599-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="d6599-274">Confronto tra proprietà chiave</span><span class="sxs-lookup"><span data-stu-id="d6599-274">Comparing key properties</span></span>

<span data-ttu-id="d6599-275">Oltre ai confronti di uguaglianza, è necessario ordinare anche i valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="d6599-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="d6599-276">Questo è importante per evitare deadlock quando si aggiornano più entità in una singola chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="d6599-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="d6599-277">Tutti i tipi utilizzati per le proprietà di chiave primaria, alternativa o esterna, nonché quelli utilizzati per gli indici univoci, devono implementare <xref:System.IComparable%601> e <xref:System.IEquatable%601> .</span><span class="sxs-lookup"><span data-stu-id="d6599-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="d6599-278">I tipi normalmente usati come chiavi (int, Guid, String e così via) supportano già tali interfacce.</span><span class="sxs-lookup"><span data-stu-id="d6599-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="d6599-279">I tipi di chiave personalizzati possono aggiungere queste interfacce.</span><span class="sxs-lookup"><span data-stu-id="d6599-279">Custom key types may to add these interfaces.</span></span>
