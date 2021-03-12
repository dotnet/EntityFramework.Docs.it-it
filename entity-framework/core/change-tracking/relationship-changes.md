---
title: Modifica di chiavi esterne e spostamenti-EF Core
description: Come modificare le relazioni tra le entità modificando le chiavi esterne e le navigazioni
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: 5d3a68a40ed8bd112c19bdaed466071b03e30a66
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024407"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="c4924-103">Modifica di chiavi esterne e spostamenti</span><span class="sxs-lookup"><span data-stu-id="c4924-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="c4924-104">Panoramica delle chiavi esterne e delle navigazioni</span><span class="sxs-lookup"><span data-stu-id="c4924-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="c4924-105">Le relazioni in un modello di Entity Framework Core (EF Core) sono rappresentate mediante chiavi esterne (FKs).</span><span class="sxs-lookup"><span data-stu-id="c4924-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="c4924-106">Un FK è costituito da una o più proprietà nell'entità dipendente o figlio nella relazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="c4924-107">Questa entità dipendente/figlio è associata a un'entità principale/padre specificata quando i valori delle proprietà di chiave esterna nell'oggetto dipendente/figlio corrispondono ai valori delle proprietà di chiave alternativa o primaria (PK) nell'entità/padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="c4924-108">Le chiavi esterne sono un metodo efficace per archiviare e modificare le relazioni nel database, ma non sono molto intuitive quando si utilizzano più entità correlate nel codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="c4924-109">Pertanto, la maggior parte dei modelli di EF Core anche livelli di "navigazione" sulla rappresentazione FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="c4924-110">Gli spostamenti formano riferimenti C#/.NET tra istanze di entità che riflettono le associazioni individuate mediante la corrispondenza dei valori di chiave esterna con valori di chiave primari o alternativi.</span><span class="sxs-lookup"><span data-stu-id="c4924-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="c4924-111">Le esplorazioni possono essere utilizzate su entrambi i lati della relazione, solo su un lato, lasciando solo la proprietà FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="c4924-112">La proprietà FK può essere nascosta rendendola una [proprietà shadow](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="c4924-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="c4924-113">Per ulteriori informazioni sulle relazioni di modellazione, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="c4924-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="c4924-114">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="c4924-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="c4924-115">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="c4924-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="c4924-116">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span><span class="sxs-lookup"><span data-stu-id="c4924-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="c4924-117">Modello di esempio</span><span class="sxs-lookup"><span data-stu-id="c4924-117">Example model</span></span>

<span data-ttu-id="c4924-118">Il modello seguente contiene quattro tipi di entità con relazioni tra di essi.</span><span class="sxs-lookup"><span data-stu-id="c4924-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="c4924-119">I commenti nel codice indicano quali proprietà sono chiavi esterne, chiavi primarie e spostamenti.</span><span class="sxs-lookup"><span data-stu-id="c4924-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

<span data-ttu-id="c4924-120">Le tre relazioni in questo modello sono:</span><span class="sxs-lookup"><span data-stu-id="c4924-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="c4924-121">Ogni blog può avere molti post (uno-a-molti):</span><span class="sxs-lookup"><span data-stu-id="c4924-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="c4924-122">`Blog` è il principale/padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="c4924-123">`Post` è l'oggetto dipendente/figlio.</span><span class="sxs-lookup"><span data-stu-id="c4924-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="c4924-124">Contiene la proprietà FK `Post.BlogId` , il cui valore deve corrispondere al `Blog.Id` valore PK del blog correlato.</span><span class="sxs-lookup"><span data-stu-id="c4924-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="c4924-125">`Post.Blog` è una navigazione di riferimento da un post al Blog associato.</span><span class="sxs-lookup"><span data-stu-id="c4924-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="c4924-126">`Post.Blog` è la navigazione inversa per `Blog.Posts` .</span><span class="sxs-lookup"><span data-stu-id="c4924-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="c4924-127">`Blog.Posts` è una navigazione di raccolta da un Blog a tutti i post associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="c4924-128">`Blog.Posts` è la navigazione inversa per `Post.Blog` .</span><span class="sxs-lookup"><span data-stu-id="c4924-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="c4924-129">Ogni blog può avere un solo asset (uno-a-uno):</span><span class="sxs-lookup"><span data-stu-id="c4924-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="c4924-130">`Blog` è il principale/padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="c4924-131">`BlogAssets` è l'oggetto dipendente/figlio.</span><span class="sxs-lookup"><span data-stu-id="c4924-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="c4924-132">Contiene la proprietà FK `BlogAssets.BlogId` , il cui valore deve corrispondere al `Blog.Id` valore PK del blog correlato.</span><span class="sxs-lookup"><span data-stu-id="c4924-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="c4924-133">`BlogAssets.Blog` è una navigazione di riferimento dagli asset al Blog associato.</span><span class="sxs-lookup"><span data-stu-id="c4924-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="c4924-134">`BlogAssets.Blog` è la navigazione inversa per `Blog.Assets` .</span><span class="sxs-lookup"><span data-stu-id="c4924-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="c4924-135">`Blog.Assets` è una navigazione di riferimento dal Blog agli asset associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="c4924-136">`Blog.Assets` è la navigazione inversa per `BlogAssets.Blog` .</span><span class="sxs-lookup"><span data-stu-id="c4924-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="c4924-137">Ogni post può avere molti tag e ogni tag può avere molti post (many-to-many):</span><span class="sxs-lookup"><span data-stu-id="c4924-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="c4924-138">Le relazioni many-to-many sono un ulteriore livello rispetto alle relazioni 2 1-to-many.</span><span class="sxs-lookup"><span data-stu-id="c4924-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="c4924-139">Le relazioni molti-a-molti sono descritte più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="c4924-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="c4924-140">`Post.Tags` è una navigazione della raccolta da un post a tutti i tag associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="c4924-141">`Post.Tags` è la navigazione inversa per `Tag.Posts` .</span><span class="sxs-lookup"><span data-stu-id="c4924-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="c4924-142">`Tag.Posts` è una navigazione della raccolta da un tag a tutti i post associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="c4924-143">`Tag.Posts` è la navigazione inversa per `Post.Tags` .</span><span class="sxs-lookup"><span data-stu-id="c4924-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="c4924-144">Per ulteriori informazioni sulla modellazione e la configurazione delle relazioni, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="c4924-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="c4924-145">Correzione di relazioni</span><span class="sxs-lookup"><span data-stu-id="c4924-145">Relationship fixup</span></span>

<span data-ttu-id="c4924-146">EF Core mantiene le navigazioni in allineamento con i valori di chiave esterna e viceversa.</span><span class="sxs-lookup"><span data-stu-id="c4924-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="c4924-147">Ovvero, se un valore di chiave esterna viene modificato in modo da fare riferimento a un'entità principale/padre diversa, le navigazioni vengono aggiornate in base a questa modifica.</span><span class="sxs-lookup"><span data-stu-id="c4924-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="c4924-148">Analogamente, se viene modificata una navigazione, i valori di chiave esterna delle entità interessati vengono aggiornati per riflettere questa modifica.</span><span class="sxs-lookup"><span data-stu-id="c4924-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="c4924-149">Questo metodo è denominato "correzione della relazione".</span><span class="sxs-lookup"><span data-stu-id="c4924-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="c4924-150">Correzione per query</span><span class="sxs-lookup"><span data-stu-id="c4924-150">Fixup by query</span></span>

<span data-ttu-id="c4924-151">La correzione viene eseguita per prima quando viene eseguita una query sulle entità dal database.</span><span class="sxs-lookup"><span data-stu-id="c4924-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="c4924-152">Il database dispone solo di valori di chiave esterna, pertanto quando EF Core crea un'istanza di entità dal database usa i valori di chiave esterna per impostare le esplorazioni dei riferimenti e aggiungere entità alle spostamenti della raccolta in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="c4924-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="c4924-153">Si consideri, ad esempio, una query per i Blog e i post e gli asset associati:</span><span class="sxs-lookup"><span data-stu-id="c4924-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="c4924-154">Per ogni Blog, EF Core creerà prima un' `Blog` istanza.</span><span class="sxs-lookup"><span data-stu-id="c4924-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="c4924-155">Quindi, quando ogni post viene caricato dal database, la relativa `Post.Blog` navigazione di riferimento viene impostata in modo da puntare al Blog associato.</span><span class="sxs-lookup"><span data-stu-id="c4924-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="c4924-156">Analogamente, il post viene aggiunto alla `Blog.Posts` navigazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="c4924-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="c4924-157">Lo stesso accade con `BlogAssets` , ad eccezione del fatto che entrambi gli spostamenti sono riferimenti.</span><span class="sxs-lookup"><span data-stu-id="c4924-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="c4924-158">La `Blog.Assets` navigazione viene impostata in modo da puntare all'istanza di asset e la `BlogAsserts.Blog` navigazione viene impostata in modo da puntare all'istanza del Blog.</span><span class="sxs-lookup"><span data-stu-id="c4924-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="c4924-159">Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa query vengono visualizzati due Blog, ognuno con un asset e due post rilevati:</span><span class="sxs-lookup"><span data-stu-id="c4924-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="c4924-160">La visualizzazione debug Mostra sia i valori chiave che gli spostamenti.</span><span class="sxs-lookup"><span data-stu-id="c4924-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="c4924-161">Le navigazioni vengono visualizzate utilizzando i valori di chiave primaria delle entità correlate.</span><span class="sxs-lookup"><span data-stu-id="c4924-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="c4924-162">Nell'output precedente, ad esempio, viene `Posts: [{Id: 1}, {Id: 2}]` indicato che la `Blog.Posts` navigazione della raccolta contiene due post correlati rispettivamente con chiavi primarie 1 e 2.</span><span class="sxs-lookup"><span data-stu-id="c4924-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="c4924-163">Analogamente, per ogni post associato al primo Blog, la `Blog: {Id: 1}` riga indica che la `Post.Blog` navigazione fa riferimento al Blog con chiave primaria 1.</span><span class="sxs-lookup"><span data-stu-id="c4924-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="c4924-164">Correzione delle entità rilevate localmente</span><span class="sxs-lookup"><span data-stu-id="c4924-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="c4924-165">La correzione delle relazioni si verifica anche tra le entità restituite da una query di rilevamento e le entità già rilevate da DbContext.</span><span class="sxs-lookup"><span data-stu-id="c4924-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="c4924-166">Si consideri, ad esempio, l'esecuzione di tre query separate per Blog, post e asset:</span><span class="sxs-lookup"><span data-stu-id="c4924-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
<span data-ttu-id="c4924-167">Una volta esaminate le visualizzazioni di debug, dopo la prima query vengono rilevati solo i due Blog:</span><span class="sxs-lookup"><span data-stu-id="c4924-167">Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

<span data-ttu-id="c4924-168">Le esplorazioni dei `Blog.Assets` riferimenti sono null e le esplorazioni della `Blog.Posts` raccolta sono vuote perché nessuna entità associata è attualmente rilevata dal contesto.</span><span class="sxs-lookup"><span data-stu-id="c4924-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="c4924-169">Dopo la seconda query, le esplorazioni dei `Blogs.Assets` riferimenti sono state corrette per puntare alle nuove istanze rilevate `BlogAsset` .</span><span class="sxs-lookup"><span data-stu-id="c4924-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="c4924-170">Analogamente, le esplorazioni dei `BlogAssets.Blog` riferimenti sono impostate in modo da puntare all'istanza già rilevata appropriata `Blog` .</span><span class="sxs-lookup"><span data-stu-id="c4924-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

<span data-ttu-id="c4924-171">Infine, dopo la terza query, le esplorazioni della `Blog.Posts` raccolta ora contengono tutti i post correlati e i `Post.Blog` riferimenti puntano all' `Blog` istanza appropriata:</span><span class="sxs-lookup"><span data-stu-id="c4924-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="c4924-172">Si tratta dello stesso stato finale come è stato fatto con la singola query originale, dal momento che EF Core ha risolto le spostamenti durante il rilevamento delle entità, anche quando provengono da più query diverse.</span><span class="sxs-lookup"><span data-stu-id="c4924-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="c4924-173">La correzione non comporta mai la restituzione di più dati dal database.</span><span class="sxs-lookup"><span data-stu-id="c4924-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="c4924-174">Connette solo le entità già restituite dalla query o già rilevate da DbContext.</span><span class="sxs-lookup"><span data-stu-id="c4924-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="c4924-175">Per informazioni sulla gestione dei duplicati durante la serializzazione di entità, vedere [risoluzione delle identità in EF Core](xref:core/change-tracking/identity-resolution) .</span><span class="sxs-lookup"><span data-stu-id="c4924-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="c4924-176">Modifica delle relazioni mediante le navigazioni</span><span class="sxs-lookup"><span data-stu-id="c4924-176">Changing relationships using navigations</span></span>

<span data-ttu-id="c4924-177">Il modo più semplice per modificare la relazione tra due entità consiste nel manipolare una navigazione, lasciando EF Core per correggere i valori di navigazione inversa e FK in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="c4924-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="c4924-178">Puoi farlo in due modi:</span><span class="sxs-lookup"><span data-stu-id="c4924-178">This can be done by:</span></span>

- <span data-ttu-id="c4924-179">Aggiunta o rimozione di un'entità da una navigazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="c4924-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="c4924-180">Modifica di una navigazione di riferimento in modo che punti a un'entità diversa o impostandola su null.</span><span class="sxs-lookup"><span data-stu-id="c4924-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="c4924-181">Aggiunta o rimozione dall'esplorazione della raccolta</span><span class="sxs-lookup"><span data-stu-id="c4924-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="c4924-182">Si sposti, ad esempio, uno dei post del Blog di Visual Studio nel Blog di .NET.</span><span class="sxs-lookup"><span data-stu-id="c4924-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="c4924-183">Questa operazione richiede prima di tutto il caricamento dei Blog e dei post e quindi lo spostamento del post dalla raccolta di navigazione in un Blog alla raccolta di navigazione nell'altro Blog:</span><span class="sxs-lookup"><span data-stu-id="c4924-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <span data-ttu-id="c4924-184"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Qui è necessaria una chiamata a perché l'accesso alla visualizzazione di debug non causa il [rilevamento automatico delle modifiche](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="c4924-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="c4924-185">Questa è la visualizzazione di debug stampata dopo l'esecuzione del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="c4924-185">This is the debug view printed after running the code above:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="c4924-186">`Blog.Posts`Per la navigazione nel Blog di .NET sono ora presenti tre post ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ).</span><span class="sxs-lookup"><span data-stu-id="c4924-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="c4924-187">Analogamente, la `Blog.Posts` navigazione nel Blog di Visual Studio ha solo un post ( `Posts: [{Id: 4}]` ).</span><span class="sxs-lookup"><span data-stu-id="c4924-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="c4924-188">Questa operazione è prevista perché il codice ha modificato in modo esplicito queste raccolte.</span><span class="sxs-lookup"><span data-stu-id="c4924-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="c4924-189">Più interessante, anche se il codice non ha modificato in modo esplicito la `Post.Blog` navigazione, è stato corretto per puntare al Blog di Visual Studio ( `Blog: {Id: 1}` ).</span><span class="sxs-lookup"><span data-stu-id="c4924-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="c4924-190">Inoltre, il `Post.BlogId` valore della chiave esterna è stato aggiornato in modo che corrisponda al valore di chiave primaria del Blog .NET.</span><span class="sxs-lookup"><span data-stu-id="c4924-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="c4924-191">Questa modifica apportata al valore FK in viene salvata in modo permanente nel database quando viene chiamato SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="c4924-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="c4924-192">Modifica delle navigazione di riferimento</span><span class="sxs-lookup"><span data-stu-id="c4924-192">Changing reference navigations</span></span>

<span data-ttu-id="c4924-193">Nell'esempio precedente, un post è stato spostato da un Blog a un altro modificando la navigazione della raccolta dei post in ogni blog.</span><span class="sxs-lookup"><span data-stu-id="c4924-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="c4924-194">La stessa operazione può essere eseguita modificando invece la `Post.Blog` navigazione di riferimento in modo che punti al nuovo blog.</span><span class="sxs-lookup"><span data-stu-id="c4924-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="c4924-195">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="c4924-196">La visualizzazione di debug dopo questa modifica _è identica a quella dell'_ esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="c4924-197">Questo perché EF Core ha rilevato la modifica della navigazione di riferimento e quindi ha risolto le spostamenti della raccolta e il valore FK in modo che corrispondano.</span><span class="sxs-lookup"><span data-stu-id="c4924-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="c4924-198">Modifica di relazioni con valori di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="c4924-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="c4924-199">Nella sezione precedente, le relazioni sono state modificate dalle navigazioni che lasciano i valori di chiave esterna per essere aggiornati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="c4924-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="c4924-200">Si tratta della modalità consigliata per modificare le relazioni in EF Core.</span><span class="sxs-lookup"><span data-stu-id="c4924-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="c4924-201">Tuttavia, è anche possibile modificare direttamente i valori FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="c4924-202">Ad esempio, è possibile spostare un post da un Blog a un altro modificando il `Post.BlogId` valore della chiave esterna:</span><span class="sxs-lookup"><span data-stu-id="c4924-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="c4924-203">Si noti che questa operazione è molto simile alla modifica della navigazione dei riferimenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="c4924-204">La visualizzazione di debug dopo questa modifica è _identica a_ quella del caso dei due esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="c4924-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="c4924-205">Questo perché EF Core ha rilevato la modifica del valore FK, quindi è stato corretto il riferimento e le navigazioni della raccolta in modo che corrispondano.</span><span class="sxs-lookup"><span data-stu-id="c4924-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="c4924-206">Non scrivere codice per modificare tutti gli spostamenti e i valori FK ogni volta che una relazione viene modificata.</span><span class="sxs-lookup"><span data-stu-id="c4924-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="c4924-207">Tale codice è più complesso e deve garantire modifiche coerenti alle chiavi esterne e agli spostamenti in ogni caso.</span><span class="sxs-lookup"><span data-stu-id="c4924-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="c4924-208">Se possibile, è sufficiente modificare una sola navigazione oppure entrambe le navigazioni.</span><span class="sxs-lookup"><span data-stu-id="c4924-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="c4924-209">Se necessario, è sufficiente modificare i valori FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="c4924-210">Evitare di modificare sia gli spostamenti che i valori FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="c4924-211">Correzione per entità aggiunte o eliminate</span><span class="sxs-lookup"><span data-stu-id="c4924-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="c4924-212">Aggiunta a una navigazione della raccolta</span><span class="sxs-lookup"><span data-stu-id="c4924-212">Adding to a collection navigation</span></span>

<span data-ttu-id="c4924-213">EF Core esegue le azioni seguenti quando [rileva](xref:core/change-tracking/change-detection) che è stata aggiunta una nuova entità dipendente/figlio a una navigazione della raccolta:</span><span class="sxs-lookup"><span data-stu-id="c4924-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="c4924-214">Se l'entità non viene rilevata, viene rilevata.</span><span class="sxs-lookup"><span data-stu-id="c4924-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="c4924-215">(L'entità si trova in genere nello `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="c4924-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="c4924-216">Tuttavia, se il tipo di entità è configurato per usare le chiavi generate e il valore della chiave primaria è impostato, l'entità viene rilevata nello `Unchanged` stato.</span><span class="sxs-lookup"><span data-stu-id="c4924-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="c4924-217">Se l'entità è associata a un'entità o a un elemento padre diverso, la relazione è grave.</span><span class="sxs-lookup"><span data-stu-id="c4924-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="c4924-218">L'entità viene associata al principale/padre che possiede la navigazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="c4924-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="c4924-219">Gli spostamenti e i valori di chiave esterna sono corretti per tutte le entità di cui è stato richiesto.</span><span class="sxs-lookup"><span data-stu-id="c4924-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="c4924-220">In base a questo, possiamo notare che per spostare un post da un Blog a un altro non è necessario rimuoverlo dalla precedente navigazione della raccolta prima di aggiungerlo al nuovo.</span><span class="sxs-lookup"><span data-stu-id="c4924-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="c4924-221">Il codice dell'esempio precedente può quindi essere modificato da:</span><span class="sxs-lookup"><span data-stu-id="c4924-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="c4924-222">Con:</span><span class="sxs-lookup"><span data-stu-id="c4924-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="c4924-223">EF Core rileva che il post è stato aggiunto a un nuovo blog e lo rimuove automaticamente dalla raccolta nel primo Blog.</span><span class="sxs-lookup"><span data-stu-id="c4924-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="c4924-224">Rimozione da una navigazione della raccolta</span><span class="sxs-lookup"><span data-stu-id="c4924-224">Removing from a collection navigation</span></span>

<span data-ttu-id="c4924-225">La rimozione di un'entità dipendente/figlio dall'esplorazione della raccolta del principale/padre causa il troncamento della relazione a tale entità/elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="c4924-226">Ciò che accade dopo dipende dal fatto che la relazione sia facoltativa o richiesta.</span><span class="sxs-lookup"><span data-stu-id="c4924-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="c4924-227">Relazioni facoltative</span><span class="sxs-lookup"><span data-stu-id="c4924-227">Optional relationships</span></span>

<span data-ttu-id="c4924-228">Per impostazione predefinita, per le relazioni facoltative, il valore della chiave esterna è impostato su null.</span><span class="sxs-lookup"><span data-stu-id="c4924-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="c4924-229">Ciò significa che il dipendente/figlio non è più associato ad _alcuna_ entità/elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="c4924-230">Ad esempio, caricare un blog e post, quindi rimuovere uno dei post dall' `Blog.Posts` esplorazione della raccolta:</span><span class="sxs-lookup"><span data-stu-id="c4924-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="c4924-231">Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa modifica, viene visualizzato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="c4924-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="c4924-232">`Post.BlogId`FK è stato impostato su null ( `BlogId: <null> FK Modified Originally 1` )</span><span class="sxs-lookup"><span data-stu-id="c4924-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="c4924-233">La `Post.Blog` navigazione di riferimento è stata impostata su null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="c4924-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="c4924-234">Il post è stato rimosso dalla `Blog.Posts` navigazione della raccolta ( `Posts: [{Id: 1}]` )</span><span class="sxs-lookup"><span data-stu-id="c4924-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="c4924-235">Si noti che il post _non_ è contrassegnato come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="c4924-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="c4924-236">È contrassegnato come in `Modified` modo che il valore FK nel database venga impostato su null quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c4924-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="c4924-237">Relazioni obbligatorie</span><span class="sxs-lookup"><span data-stu-id="c4924-237">Required relationships</span></span>

<span data-ttu-id="c4924-238">L'impostazione del valore FK su null non è consentita (e in genere non è possibile) per le relazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="c4924-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="c4924-239">Pertanto, il troncamento di una relazione obbligatoria significa che l'entità dipendente/figlio deve avere un nuovo elemento padre in una nuova entità/padre oppure essere rimossa dal database quando viene chiamato SaveChanges per evitare la violazione di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c4924-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="c4924-240">Questa operazione è nota come "eliminazione di orfani" ed è il comportamento predefinito in EF Core per le relazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="c4924-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="c4924-241">È ad esempio possibile modificare la relazione tra Blog e post in modo che sia necessario, quindi eseguire lo stesso codice dell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="c4924-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="c4924-242">Esaminando la visualizzazione di debug dopo questa modifica, viene visualizzato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="c4924-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="c4924-243">Il post è stato contrassegnato in modo tale `Deleted` che verrà eliminato dal database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c4924-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="c4924-244">La `Post.Blog` navigazione di riferimento è stata impostata su null ( `Blog: <null>` ).</span><span class="sxs-lookup"><span data-stu-id="c4924-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="c4924-245">Il post è stato rimosso dalla `Blog.Posts` navigazione della raccolta ( `Posts: [{Id: 1}]` ).</span><span class="sxs-lookup"><span data-stu-id="c4924-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="c4924-246">Si noti che `Post.BlogId` rimane invariato perché per una relazione obbligatoria non può essere impostato su null.</span><span class="sxs-lookup"><span data-stu-id="c4924-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="c4924-247">La chiamata a SaveChanges comporta l'eliminazione del post orfano:</span><span class="sxs-lookup"><span data-stu-id="c4924-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="c4924-248">Elimina la tempistica degli orfani e il nuovo elemento padre</span><span class="sxs-lookup"><span data-stu-id="c4924-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="c4924-249">Per impostazione predefinita, contrassegnare gli orfani come avviene non appena `Deleted` viene [rilevata](xref:core/change-tracking/change-detection)la modifica della relazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="c4924-250">Tuttavia, questo processo può essere posticipato fino a quando non viene effettivamente chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c4924-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="c4924-251">Questa operazione può essere utile per evitare di rendere orfani le entità che sono state rimosse da un'entità/elemento padre, ma verrà riassociato un nuovo elemento padre con una nuova entità/padre prima della chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c4924-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="c4924-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> viene usato per impostare questa temporizzazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="c4924-253">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-253">For example:</span></span>

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

<span data-ttu-id="c4924-254">Dopo la rimozione del post dalla prima raccolta, l'oggetto non è contrassegnato come `Deleted` l'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="c4924-255">EF Core verifica invece che la relazione venga interrotta _anche se si tratta di una relazione obbligatoria_.</span><span class="sxs-lookup"><span data-stu-id="c4924-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="c4924-256">Il valore FK viene considerato null da EF Core anche se non può essere effettivamente null perché il tipo non ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="c4924-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="c4924-257">Questa operazione è nota come "null concettuale".</span><span class="sxs-lookup"><span data-stu-id="c4924-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="c4924-258">La chiamata a SaveChanges in questo momento comporta l'eliminazione del post orfano.</span><span class="sxs-lookup"><span data-stu-id="c4924-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="c4924-259">Tuttavia, se come nell'esempio precedente, post è associato a un nuovo blog prima della chiamata a SaveChanges, il post verrà corretto in modo appropriato per il nuovo blog e non sarà più considerato orfano:</span><span class="sxs-lookup"><span data-stu-id="c4924-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="c4924-260">Il metodo SaveChanges chiamato a questo punto aggiornerà il post nel database anziché eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="c4924-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="c4924-261">È anche possibile disattivare l'eliminazione automatica degli orfani.</span><span class="sxs-lookup"><span data-stu-id="c4924-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="c4924-262">Questa operazione genererà un'eccezione se viene chiamato SaveChanges mentre è in corso il rilevamento di un orfano.</span><span class="sxs-lookup"><span data-stu-id="c4924-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="c4924-263">Ad esempio, il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c4924-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="c4924-264">Genererà questa eccezione:</span><span class="sxs-lookup"><span data-stu-id="c4924-264">Will throw this exception:</span></span>

> <span data-ttu-id="c4924-265">System. InvalidOperationException: l'associazione tra le entità' Blog ' è post ' con il valore di chiave ' {BlogId: 1}' è stata interrotta, ma la relazione è contrassegnata come obbligatoria o è richiesta in modo implicito perché la chiave esterna non ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="c4924-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="c4924-266">Se l'entità dipendente/figlio deve essere eliminata quando una relazione obbligatoria è grave, configurare la relazione per l'utilizzo delle eliminazioni a catena.</span><span class="sxs-lookup"><span data-stu-id="c4924-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="c4924-267">L'eliminazione di orfani, nonché di eliminazioni a cascata, può essere forzata in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c4924-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c4924-268">Se si combina questa operazione con l'impostazione dell'intervallo di eliminazione orfano `Never` , gli orfani non verranno mai eliminati, a meno che non venga esplicitamente richiesto EF core.</span><span class="sxs-lookup"><span data-stu-id="c4924-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="c4924-269">Modifica di una navigazione di riferimento</span><span class="sxs-lookup"><span data-stu-id="c4924-269">Changing a reference navigation</span></span>

<span data-ttu-id="c4924-270">La modifica della navigazione di riferimento di una relazione uno-a-molti ha lo stesso effetto della modifica della navigazione della raccolta nell'altra entità finale della relazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="c4924-271">L'impostazione della navigazione di riferimento di dipendente/figlio su null equivale a rimuovere l'entità dalla navigazione della raccolta del principale/padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="c4924-272">Tutte le correzioni e le modifiche al database vengono apportate come descritto nella sezione precedente, inclusa la creazione di un'entità orfana se la relazione è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="c4924-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="c4924-273">Relazioni uno-a-uno facoltative</span><span class="sxs-lookup"><span data-stu-id="c4924-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="c4924-274">Per le relazioni uno-a-uno, la modifica di una navigazione di riferimento causa la reimpostazione di qualsiasi relazione precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="c4924-275">Per le relazioni facoltative, questo significa che il valore FK sul dipendente/figlio correlato in precedenza è impostato su null.</span><span class="sxs-lookup"><span data-stu-id="c4924-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="c4924-276">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="c4924-277">La visualizzazione debug prima di chiamare SaveChanges Mostra che i nuovi asset hanno sostituito gli asset esistenti, che ora è contrassegnato come `Modified` con un `BlogAssets.BlogId` valore FK null:</span><span class="sxs-lookup"><span data-stu-id="c4924-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

<span data-ttu-id="c4924-278">Ciò comporta un aggiornamento e un inserimento quando viene chiamato SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="c4924-278">This results in an update and an insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="c4924-279">Relazioni uno-a-uno obbligatorie</span><span class="sxs-lookup"><span data-stu-id="c4924-279">Required one-to-one relationships</span></span>

<span data-ttu-id="c4924-280">Eseguendo lo stesso codice dell'esempio precedente, ma questa volta con una relazione uno-a-uno obbligatoria, Mostra che l'oggetto associato in precedenza `BlogAssets` è ora contrassegnato come `Deleted` , poiché diventa un orfano quando il nuovo `BlogAssets` prende il posto:</span><span class="sxs-lookup"><span data-stu-id="c4924-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

<span data-ttu-id="c4924-281">Viene quindi generata un'istruzione Delete an e Insert quando SaveChanges viene chiamato:</span><span class="sxs-lookup"><span data-stu-id="c4924-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="c4924-282">I tempi di contrassegno degli orfani come eliminati possono essere modificati nello stesso modo in cui vengono visualizzati per le esplorazioni delle raccolte e hanno gli stessi effetti.</span><span class="sxs-lookup"><span data-stu-id="c4924-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="c4924-283">Eliminazione di un'entità</span><span class="sxs-lookup"><span data-stu-id="c4924-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="c4924-284">Relazioni facoltative</span><span class="sxs-lookup"><span data-stu-id="c4924-284">Optional relationships</span></span>

<span data-ttu-id="c4924-285">Quando un'entità è contrassegnata come `Deleted` , ad esempio chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , i riferimenti all'entità eliminata vengono rimossi dagli spostamenti di altre entità.</span><span class="sxs-lookup"><span data-stu-id="c4924-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="c4924-286">Per le relazioni facoltative, i valori FK in entità dipendenti vengono impostati su null.</span><span class="sxs-lookup"><span data-stu-id="c4924-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="c4924-287">Ad esempio, è possibile contrassegnare il Blog di Visual Studio come `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="c4924-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

<span data-ttu-id="c4924-288">Esaminare la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges Mostra:</span><span class="sxs-lookup"><span data-stu-id="c4924-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="c4924-289">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="c4924-289">Notice that:</span></span>

- <span data-ttu-id="c4924-290">Il Blog è contrassegnato come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="c4924-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="c4924-291">Gli asset correlati al Blog eliminato hanno un valore FK null ( `BlogId: <null> FK Modified Originally 2` ) e una navigazione di riferimento null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="c4924-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="c4924-292">Ogni post correlato al Blog eliminato ha un valore FK null ( `BlogId: <null> FK Modified Originally 2` ) e una navigazione di riferimento null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="c4924-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="c4924-293">Relazioni obbligatorie</span><span class="sxs-lookup"><span data-stu-id="c4924-293">Required relationships</span></span>

<span data-ttu-id="c4924-294">Il comportamento della correzione per le relazioni obbligatorie è identico a quello delle relazioni facoltative, ad eccezione del fatto che le entità dipendenti/figlio sono contrassegnate come `Deleted` poiché non possono esistere senza un'entità/elemento padre e devono essere rimosse dal database quando viene chiamato SaveChanges per evitare un'eccezione di vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c4924-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="c4924-295">Questa operazione è nota come "eliminazione a catena" ed è il comportamento predefinito in EF Core per le relazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="c4924-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="c4924-296">Se, ad esempio, si esegue lo stesso codice dell'esempio precedente, ma con una relazione obbligatoria si ottiene la seguente visualizzazione di debug prima della chiamata a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="c4924-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="c4924-297">Come previsto, i dipendenti/figli sono ora contrassegnati come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="c4924-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="c4924-298">Si noti tuttavia che le esplorazioni sulle entità eliminate _non_ sono state modificate.</span><span class="sxs-lookup"><span data-stu-id="c4924-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="c4924-299">Questo può sembrare strano, ma evita di suddividere completamente un grafo eliminato di entità, cancellando tutte le navigazioni.</span><span class="sxs-lookup"><span data-stu-id="c4924-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="c4924-300">Ovvero il Blog, l'asset e i post formano ancora un grafico delle entità anche dopo essere stati eliminati.</span><span class="sxs-lookup"><span data-stu-id="c4924-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="c4924-301">In questo modo è molto più semplice annullare l'eliminazione di un grafico di entità rispetto al caso in EF6 in cui il grafo è stato triturato.</span><span class="sxs-lookup"><span data-stu-id="c4924-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="c4924-302">Intervallo di eliminazione a catena e nuovo elemento padre</span><span class="sxs-lookup"><span data-stu-id="c4924-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="c4924-303">Per impostazione predefinita, l'eliminazione a catena viene eseguita non appena l'elemento padre/principale è contrassegnato come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="c4924-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="c4924-304">Questa operazione è identica a quella per l'eliminazione di orfani, come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c4924-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="c4924-305">Come per l'eliminazione di orfani, questo processo può essere posticipato fino a quando non viene chiamato SaveChanges o anche completamente disabilitato, impostando in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="c4924-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="c4924-306">Questa operazione è utile in modo analogo a quanto avviene per l'eliminazione di elementi orfani, inclusa la reassociazione di elementi figlio/dipendenti dopo l'eliminazione di un'entità o di un elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c4924-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="c4924-307">Le eliminazioni a catena, oltre all'eliminazione di orfani, possono essere forzate in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c4924-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c4924-308">Se si combina questa impostazione con l'impostazione dell'intervallo di eliminazione a cascata `Never` , le eliminazioni a catena verranno eseguite solo se a EF core viene esplicitamente richiesto di eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="c4924-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="c4924-309">Gli orfani a catena e eliminazione sono strettamente correlati.</span><span class="sxs-lookup"><span data-stu-id="c4924-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="c4924-310">Entrambe comportano l'eliminazione di entità dipendenti/figlio quando la relazione con l'entità/padre richiesta è grave.</span><span class="sxs-lookup"><span data-stu-id="c4924-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="c4924-311">Per l'eliminazione a catena, questo errore si verifica perché il server principale/padre viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c4924-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="c4924-312">Per gli orfani, l'entità principale/padre continua a esistere, ma non è più correlata alle entità dipendenti/figlio.</span><span class="sxs-lookup"><span data-stu-id="c4924-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="c4924-313">Relazioni molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="c4924-313">Many-to-many relationships</span></span>

<span data-ttu-id="c4924-314">Le relazioni molti-a-molti in EF Core vengono implementate mediante un'entità di join.</span><span class="sxs-lookup"><span data-stu-id="c4924-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="c4924-315">Ogni lato la relazione molti-a-molti è correlato a questa entità di join con una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="c4924-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="c4924-316">Prima di EF Core 5,0, questa entità di join doveva essere definita e mappata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="c4924-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="c4924-317">A partire da EF Core 5,0, può essere creato in modo implicito e nascosto.</span><span class="sxs-lookup"><span data-stu-id="c4924-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="c4924-318">In entrambi i casi, tuttavia, il comportamento sottostante è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="c4924-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="c4924-319">Questo comportamento sottostante verrà innanzitutto esaminato per comprendere il funzionamento del rilevamento delle relazioni molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="c4924-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="c4924-320">Funzionamento delle relazioni molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="c4924-320">How many-to-many relationships work</span></span>

<span data-ttu-id="c4924-321">Si consideri questo modello EF Core che crea una relazione molti-a-molti tra post e tag usando un tipo di entità join definito in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="c4924-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

<span data-ttu-id="c4924-322">Si noti che il `PostTag` tipo di entità join contiene due proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="c4924-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="c4924-323">In questo modello, affinché un post sia correlato a un tag, deve essere presente un'entità di join PostTag in cui il `PostTag.PostId` valore della chiave esterna corrisponde al valore della chiave `Post.Id` primaria e dove il valore della `PostTag.TagId` chiave esterna corrisponde al valore della chiave `Tag.Id` primaria.</span><span class="sxs-lookup"><span data-stu-id="c4924-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="c4924-324">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="c4924-325">Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo l'esecuzione di questo codice, viene mostrato che il post e il tag sono correlati dalla nuova `PostTag` entità join:</span><span class="sxs-lookup"><span data-stu-id="c4924-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

<span data-ttu-id="c4924-326">Si noti che gli spostamenti della raccolta in `Post` e `Tag` sono stati corretti, così come hanno le esplorazioni dei riferimenti in `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="c4924-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="c4924-327">Queste relazioni possono essere modificate tramite le navigazioni anziché i valori FK, come in tutti gli esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="c4924-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="c4924-328">Il codice precedente, ad esempio, può essere modificato per aggiungere la relazione impostando le esplorazioni dei riferimenti nell'entità di join:</span><span class="sxs-lookup"><span data-stu-id="c4924-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="c4924-329">Ciò comporta esattamente la stessa modifica a FKs e alle esplorazioni come nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="c4924-330">Ignora navigazione</span><span class="sxs-lookup"><span data-stu-id="c4924-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="c4924-331">Le esplorazioni ignorate sono state introdotte nel EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="c4924-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="c4924-332">La modifica manuale della tabella di join può essere complessa.</span><span class="sxs-lookup"><span data-stu-id="c4924-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="c4924-333">A partire da EF Core 5,0, le relazioni molti-a-molti possono essere modificate direttamente usando spostamenti speciali della raccolta che "ignorano" l'entità di join.</span><span class="sxs-lookup"><span data-stu-id="c4924-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="c4924-334">Ad esempio, è possibile aggiungere due navigazioni Skip al modello precedente. uno da post a tag e l'altro da tag a post:</span><span class="sxs-lookup"><span data-stu-id="c4924-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

<span data-ttu-id="c4924-335">Questa relazione molti-a-molti richiede la seguente configurazione per garantire che le esplorazioni ignorate e le normali esplorazioni siano tutte usate per la stessa relazione molti-a-molti:</span><span class="sxs-lookup"><span data-stu-id="c4924-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

<span data-ttu-id="c4924-336">Per ulteriori informazioni sul mapping di relazioni molti-a-molti, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="c4924-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="c4924-337">Ignorare le esplorazioni e comportarsi come le normali spostamenti della raccolta.</span><span class="sxs-lookup"><span data-stu-id="c4924-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="c4924-338">Tuttavia, il modo in cui funzionano con i valori di chiave esterna è diverso.</span><span class="sxs-lookup"><span data-stu-id="c4924-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="c4924-339">Associare un post a un tag, ma questa volta usando una navigazione Skip:</span><span class="sxs-lookup"><span data-stu-id="c4924-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="c4924-340">Si noti che questo codice non usa l'entità join.</span><span class="sxs-lookup"><span data-stu-id="c4924-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="c4924-341">Aggiunge invece un'entità a una raccolta di navigazione nello stesso modo in cui verrebbe eseguita se si trattasse di una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="c4924-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="c4924-342">La visualizzazione di debug risultante è essenzialmente identica a quella precedente:</span><span class="sxs-lookup"><span data-stu-id="c4924-342">The resulting debug view is essentially the same as before:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

<span data-ttu-id="c4924-343">Si noti che un'istanza dell' `PostTag` entità join è stata creata automaticamente con i valori FK impostati sui valori PK del tag e post che sono ora associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="c4924-344">Tutte le normali esplorazioni di riferimento e raccolta sono state risolte in base a questi valori FK.</span><span class="sxs-lookup"><span data-stu-id="c4924-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="c4924-345">Inoltre, poiché questo modello contiene le navigazioni Skip, anche queste sono state risolte.</span><span class="sxs-lookup"><span data-stu-id="c4924-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="c4924-346">In particolare, anche se è stato aggiunto il tag all' `Post.Tags` esplorazione Skip, la `Tag.Posts` navigazione inversa ignora sull'altro lato di questa relazione è stata anche risolta in modo da contenere il post associato.</span><span class="sxs-lookup"><span data-stu-id="c4924-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="c4924-347">Vale la pena notare che le relazioni molti-a-molti sottostanti possono comunque essere modificate direttamente anche quando le esplorazioni Skip sono state sovrapposte al livello superiore.</span><span class="sxs-lookup"><span data-stu-id="c4924-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="c4924-348">Ad esempio, il tag e il post potrebbero essere associati come prima introduzione a Skip Navigations:</span><span class="sxs-lookup"><span data-stu-id="c4924-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="c4924-349">In alternativa, usare i valori FK:</span><span class="sxs-lookup"><span data-stu-id="c4924-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="c4924-350">Questa operazione comporterà la corretta correzione degli spostamenti di esplorazione, ottenendo così lo stesso output della visualizzazione di debug dell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c4924-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="c4924-351">Ignora solo le navigazioni</span><span class="sxs-lookup"><span data-stu-id="c4924-351">Skip navigations only</span></span>

<span data-ttu-id="c4924-352">Nella sezione precedente sono state aggiunte le funzionalità Skip Navigation, _oltre a_ definire completamente le due relazioni uno-a-molti sottostanti.</span><span class="sxs-lookup"><span data-stu-id="c4924-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="c4924-353">Questa operazione è utile per illustrare cosa accade ai valori FK, ma spesso non è necessario.</span><span class="sxs-lookup"><span data-stu-id="c4924-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="c4924-354">È invece possibile definire la relazione molti-a-molti utilizzando _solo le navigazioni Skip_.</span><span class="sxs-lookup"><span data-stu-id="c4924-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="c4924-355">Questo è il modo in cui viene definita la relazione molti-a-molti nel modello nella parte superiore di questo documento.</span><span class="sxs-lookup"><span data-stu-id="c4924-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="c4924-356">Usando questo modello, è possibile associare di nuovo un post e un tag aggiungendo un post a `Tag.Posts` Skip Navigation (o, in alternativa, aggiungendo un tag a `Post.Tags` Skip Navigation):</span><span class="sxs-lookup"><span data-stu-id="c4924-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="c4924-357">Esaminando la visualizzazione debug dopo avere apportato questa modifica, si rivela che EF Core ha creato un'istanza di `Dictionary<string, object>` per rappresentare l'entità di join.</span><span class="sxs-lookup"><span data-stu-id="c4924-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="c4924-358">Questa entità di join contiene `PostsId` entrambe `TagsId` le proprietà di chiave esterna che sono state impostate in modo da corrispondere ai valori PK del post e del tag associati.</span><span class="sxs-lookup"><span data-stu-id="c4924-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

<span data-ttu-id="c4924-359">Per ulteriori informazioni sulle entità di join implicite e sull'utilizzo di tipi di entità, vedere [relazioni](xref:core/modeling/relationships) `Dictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="c4924-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c4924-360">Il tipo CLR utilizzato per i tipi di entità join per convenzione può cambiare nelle versioni future per migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c4924-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="c4924-361">Non dipendono dal tipo di join `Dictionary<string, object>` , a meno che non sia stato configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="c4924-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="c4924-362">Unire entità con payload</span><span class="sxs-lookup"><span data-stu-id="c4924-362">Join entities with payloads</span></span>

<span data-ttu-id="c4924-363">Fino a questo punto, tutti gli esempi hanno usato un tipo di entità di join (esplicito o implicito) che contiene solo le due proprietà di chiave esterna necessarie per la relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="c4924-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="c4924-364">Nessuno di questi valori FK deve essere impostato in modo esplicito dall'applicazione quando si modificano le relazioni perché i relativi valori provengono dalle proprietà di chiave primaria delle entità correlate.</span><span class="sxs-lookup"><span data-stu-id="c4924-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="c4924-365">Questo consente EF Core di creare istanze dell'entità di join senza dati mancanti.</span><span class="sxs-lookup"><span data-stu-id="c4924-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="c4924-366">Payload con valori generati</span><span class="sxs-lookup"><span data-stu-id="c4924-366">Payloads with generated values</span></span>

<span data-ttu-id="c4924-367">EF Core supporta l'aggiunta di proprietà aggiuntive al tipo di entità join.</span><span class="sxs-lookup"><span data-stu-id="c4924-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="c4924-368">Questa operazione è nota come assegnazione dell'entità di join a un "payload".</span><span class="sxs-lookup"><span data-stu-id="c4924-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="c4924-369">Ad esempio, aggiungere `TaggedOn` la proprietà all' `PostTag` entità join:</span><span class="sxs-lookup"><span data-stu-id="c4924-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="c4924-370">Questa proprietà del payload non verrà impostata quando EF Core crea un'istanza di entità di join.</span><span class="sxs-lookup"><span data-stu-id="c4924-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="c4924-371">Il modo più comune per gestire questo problema consiste nell'usare le proprietà del payload con i valori generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="c4924-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="c4924-372">Ad esempio, la `TaggedOn` proprietà può essere configurata in modo da usare un timestamp generato dall'archivio quando viene inserita ogni nuova entità:</span><span class="sxs-lookup"><span data-stu-id="c4924-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

<span data-ttu-id="c4924-373">È ora possibile contrassegnare un post in modo analogo a quanto prima:</span><span class="sxs-lookup"><span data-stu-id="c4924-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="c4924-374">Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo la chiamata a SaveChanges si indica che la proprietà payload è stata impostata in modo appropriato:</span><span class="sxs-lookup"><span data-stu-id="c4924-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="c4924-375">Impostazione esplicita dei valori del payload</span><span class="sxs-lookup"><span data-stu-id="c4924-375">Explicitly setting payload values</span></span>

<span data-ttu-id="c4924-376">Seguendo l'esempio precedente, viene aggiunta una proprietà payload che non usa un valore generato automaticamente:</span><span class="sxs-lookup"><span data-stu-id="c4924-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

<span data-ttu-id="c4924-377">È ora possibile contrassegnare un post in modo analogo a quanto prima e l'entità join verrà comunque creata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="c4924-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="c4924-378">È quindi possibile accedere a questa entità utilizzando uno dei meccanismi descritti in [accesso alle entità registrate](xref:core/change-tracking/entity-entries).</span><span class="sxs-lookup"><span data-stu-id="c4924-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="c4924-379">Ad esempio, il codice seguente usa <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> per accedere all'istanza dell'entità di join:</span><span class="sxs-lookup"><span data-stu-id="c4924-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

<span data-ttu-id="c4924-380">Una volta individuata l'entità di join, è possibile modificarla in modo normale, in questo esempio, per impostare la `TaggedBy` Proprietà payload prima di chiamare SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c4924-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="c4924-381">Si noti che è necessaria una chiamata a per <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> fornire EF core la possibilità di rilevare la modifica della proprietà di navigazione e creare l'istanza dell'entità di join prima di `Find` usare.</span><span class="sxs-lookup"><span data-stu-id="c4924-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="c4924-382">Per ulteriori informazioni, vedere [rilevamento delle modifiche e notifiche](xref:core/change-tracking/change-detection) .</span><span class="sxs-lookup"><span data-stu-id="c4924-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="c4924-383">In alternativa, l'entità di join può essere creata in modo esplicito per associare un post a un tag.</span><span class="sxs-lookup"><span data-stu-id="c4924-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="c4924-384">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-384">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

<span data-ttu-id="c4924-385">Infine, un altro modo per impostare i dati del payload consiste nell'eseguire <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> l'override o l'utilizzo dell' <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> evento per elaborare le entità prima di aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="c4924-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="c4924-386">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c4924-386">For example:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
