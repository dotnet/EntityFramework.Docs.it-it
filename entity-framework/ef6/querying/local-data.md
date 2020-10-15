---
title: Dati locali-EF6
description: Dati locali in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: cff3612072ad9caa057d8589d47cc75ae32be8db
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065875"
---
# <a name="local-data"></a><span data-ttu-id="b7940-103">Dati locali</span><span class="sxs-lookup"><span data-stu-id="b7940-103">Local Data</span></span>
<span data-ttu-id="b7940-104">L'esecuzione di una query LINQ direttamente su un DbSet invierà sempre una query al database, ma è possibile accedere ai dati attualmente in memoria usando la proprietà DbSet. local.</span><span class="sxs-lookup"><span data-stu-id="b7940-104">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="b7940-105">È anche possibile accedere alle informazioni aggiuntive EF tenendo traccia delle entità usando i metodi DbContext. entry e DbContext. ChangeTracker. entrys.</span><span class="sxs-lookup"><span data-stu-id="b7940-105">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="b7940-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="b7940-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="b7940-107">Uso locale per esaminare i dati locali</span><span class="sxs-lookup"><span data-stu-id="b7940-107">Using Local to look at local data</span></span>  

<span data-ttu-id="b7940-108">La proprietà locale di DbSet consente di accedere in modo semplice alle entità del set attualmente rilevate dal contesto e non sono state contrassegnate come eliminate.</span><span class="sxs-lookup"><span data-stu-id="b7940-108">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="b7940-109">L'accesso alla proprietà locale non determina mai l'invio di una query al database.</span><span class="sxs-lookup"><span data-stu-id="b7940-109">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="b7940-110">Ciò significa che viene in genere usato dopo che una query è già stata eseguita.</span><span class="sxs-lookup"><span data-stu-id="b7940-110">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="b7940-111">Il metodo di estensione Load può essere utilizzato per eseguire una query in modo che il contesto rilevi i risultati.</span><span class="sxs-lookup"><span data-stu-id="b7940-111">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="b7940-112">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7940-112">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

<span data-ttu-id="b7940-113">Se nel database sono presenti due Blog: "ADO.NET Blog" con BlogId 1 e "The Visual Studio Blog" con BlogId 2, si potrebbe prevedere l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="b7940-113">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="b7940-114">Vengono illustrati tre punti:</span><span class="sxs-lookup"><span data-stu-id="b7940-114">This illustrates three points:</span></span>  

- <span data-ttu-id="b7940-115">Il nuovo Blog "My New Blog" è incluso nella raccolta locale anche se non è ancora stato salvato nel database.</span><span class="sxs-lookup"><span data-stu-id="b7940-115">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="b7940-116">Questo Blog presenta una chiave primaria pari a zero perché il database non ha ancora generato una chiave reale per l'entità.</span><span class="sxs-lookup"><span data-stu-id="b7940-116">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="b7940-117">Il Blog ' ADO.NET ' non è incluso nella raccolta locale anche se è ancora rilevato dal contesto.</span><span class="sxs-lookup"><span data-stu-id="b7940-117">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="b7940-118">Questo è dovuto al fatto che è stato rimosso dal DbSet, in modo da contrassegnarlo come eliminato.</span><span class="sxs-lookup"><span data-stu-id="b7940-118">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="b7940-119">Quando si usa DbSet per eseguire una query, il Blog contrassegnato per l'eliminazione (Blog di ADO.NET) è incluso nei risultati e il nuovo Blog (nuovo Blog) che non è ancora stato salvato nel database non è incluso nei risultati.</span><span class="sxs-lookup"><span data-stu-id="b7940-119">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="b7940-120">Questo perché DbSet esegue una query sul database e i risultati restituiti riflettono sempre il contenuto del database.</span><span class="sxs-lookup"><span data-stu-id="b7940-120">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="b7940-121">Uso di local per aggiungere e rimuovere entità dal contesto</span><span class="sxs-lookup"><span data-stu-id="b7940-121">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="b7940-122">La proprietà locale in DbSet restituisce un oggetto [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) con eventi collegati in modo che rimanga sincronizzato con il contenuto del contesto.</span><span class="sxs-lookup"><span data-stu-id="b7940-122">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="b7940-123">Questo significa che le entità possono essere aggiunte o rimosse dalla raccolta locale o da DbSet.</span><span class="sxs-lookup"><span data-stu-id="b7940-123">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="b7940-124">Significa anche che le query che portano nuove entità nel contesto comporteranno l'aggiornamento della raccolta locale con tali entità.</span><span class="sxs-lookup"><span data-stu-id="b7940-124">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="b7940-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7940-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

<span data-ttu-id="b7940-126">Supponendo che siano stati contrassegnati alcuni post con ' Entity-Framework ' è asp.net ', l'output potrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b7940-126">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

<span data-ttu-id="b7940-127">Vengono illustrati tre punti:</span><span class="sxs-lookup"><span data-stu-id="b7940-127">This illustrates three points:</span></span>  

- <span data-ttu-id="b7940-128">Il nuovo post ' What ' s New in EF ' aggiunto alla raccolta locale viene rilevato dal contesto nello stato Added.</span><span class="sxs-lookup"><span data-stu-id="b7940-128">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="b7940-129">Che verrà quindi inserita nel database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b7940-129">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="b7940-130">Il post rimosso dalla raccolta locale (Guida per principianti di EF) è ora contrassegnato come eliminato nel contesto.</span><span class="sxs-lookup"><span data-stu-id="b7940-130">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="b7940-131">Verrà quindi eliminato dal database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b7940-131">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="b7940-132">Il post aggiuntivo (Guida per principianti ASP.NET) caricato nel contesto con la seconda query viene aggiunto automaticamente alla raccolta locale.</span><span class="sxs-lookup"><span data-stu-id="b7940-132">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="b7940-133">Un aspetto finale da considerare per quanto riguarda local è il fatto che, poiché si tratta di una performance ObservableCollection, non è ideale per un numero elevato di entità.</span><span class="sxs-lookup"><span data-stu-id="b7940-133">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="b7940-134">Se pertanto si utilizzano migliaia di entità nel contesto, potrebbe non essere consigliabile utilizzare local.</span><span class="sxs-lookup"><span data-stu-id="b7940-134">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="b7940-135">Uso di local per WPF data binding</span><span class="sxs-lookup"><span data-stu-id="b7940-135">Using Local for WPF data binding</span></span>  

<span data-ttu-id="b7940-136">La proprietà locale in DbSet può essere utilizzata direttamente per data binding in un'applicazione WPF perché è un'istanza di ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="b7940-136">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="b7940-137">Come descritto nelle sezioni precedenti, questo significa che verrà automaticamente sincronizzato con il contenuto del contesto e il contenuto del contesto resterà sincronizzato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b7940-137">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="b7940-138">Si noti che è necessario pre-popolare la raccolta locale con i dati in modo che sia necessario eseguire il binding perché local non causa mai una query sul database.</span><span class="sxs-lookup"><span data-stu-id="b7940-138">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="b7940-139">Non si tratta di una posizione appropriata per un esempio di data binding WPF completo, ma gli elementi chiave sono:</span><span class="sxs-lookup"><span data-stu-id="b7940-139">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="b7940-140">Configurare un'origine di binding</span><span class="sxs-lookup"><span data-stu-id="b7940-140">Setup a binding source</span></span>  
- <span data-ttu-id="b7940-141">Associarlo alla proprietà locale del set</span><span class="sxs-lookup"><span data-stu-id="b7940-141">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="b7940-142">Popolare local utilizzando una query nel database.</span><span class="sxs-lookup"><span data-stu-id="b7940-142">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="b7940-143">Associazione WPF alle proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="b7940-143">WPF binding to navigation properties</span></span>  

<span data-ttu-id="b7940-144">Se si sta eseguendo data binding Master/Detail, è possibile associare la visualizzazione dettagli a una proprietà di navigazione di una delle entità.</span><span class="sxs-lookup"><span data-stu-id="b7940-144">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="b7940-145">Un modo semplice per eseguire questa operazione consiste nell'usare un oggetto ObservableCollection per la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="b7940-145">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="b7940-146">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7940-146">For example:</span></span>  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="b7940-147">Uso di local per la pulizia delle entità in SaveChanges</span><span class="sxs-lookup"><span data-stu-id="b7940-147">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="b7940-148">Nella maggior parte dei casi le entità rimosse da una proprietà di navigazione non verranno contrassegnate automaticamente come eliminate nel contesto.</span><span class="sxs-lookup"><span data-stu-id="b7940-148">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="b7940-149">Se ad esempio si rimuove un oggetto post dalla raccolta Blog. Posts, il post non verrà eliminato automaticamente quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b7940-149">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="b7940-150">Se è necessario eliminarlo, potrebbe essere necessario trovare queste entità penzoloni e contrassegnarle come eliminate prima di chiamare SaveChanges o come parte di un oggetto SaveChanges sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="b7940-150">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="b7940-151">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7940-151">For example:</span></span>  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

<span data-ttu-id="b7940-152">Il codice precedente usa la raccolta locale per trovare tutti i post e contrassegna quelli che non dispongono di un riferimento a Blog come eliminati.</span><span class="sxs-lookup"><span data-stu-id="b7940-152">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="b7940-153">La chiamata ToList è obbligatoria perché, in caso contrario, la raccolta verrà modificata dalla chiamata Remove mentre è in corso l'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="b7940-153">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="b7940-154">Nella maggior parte delle situazioni è possibile eseguire una query direttamente sulla proprietà locale senza prima usare ToList.</span><span class="sxs-lookup"><span data-stu-id="b7940-154">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="b7940-155">Utilizzo di local e tobinding per Windows Forms data binding</span><span class="sxs-lookup"><span data-stu-id="b7940-155">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="b7940-156">Windows Forms non supporta la fedeltà completa data binding utilizzando direttamente ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="b7940-156">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="b7940-157">Tuttavia, è comunque possibile usare la proprietà locale DbSet per data binding per ottenere tutti i vantaggi descritti nelle sezioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="b7940-157">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="b7940-158">Questa operazione viene eseguita tramite il metodo di estensione tobinding, che consente di creare un'implementazione di [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) supportata dalla classe ObservableCollection locale.</span><span class="sxs-lookup"><span data-stu-id="b7940-158">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="b7940-159">Non si tratta di una posizione appropriata per un Windows Forms completo data binding esempio, ma gli elementi chiave sono:</span><span class="sxs-lookup"><span data-stu-id="b7940-159">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="b7940-160">Configurare un'origine di associazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="b7940-160">Setup an object binding source</span></span>  
- <span data-ttu-id="b7940-161">Associarlo alla proprietà locale del set usando local. tobinding ()</span><span class="sxs-lookup"><span data-stu-id="b7940-161">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="b7940-162">Popolare local usando una query nel database</span><span class="sxs-lookup"><span data-stu-id="b7940-162">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="b7940-163">Ottenere informazioni dettagliate sulle entità rilevate</span><span class="sxs-lookup"><span data-stu-id="b7940-163">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="b7940-164">Molti degli esempi di questa serie usano il metodo entry per restituire un'istanza di DbEntityEntry per un'entità.</span><span class="sxs-lookup"><span data-stu-id="b7940-164">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="b7940-165">Questo oggetto entry funge quindi da punto di partenza per la raccolta di informazioni sull'entità, ad esempio lo stato corrente, nonché per l'esecuzione di operazioni sull'entità, ad esempio il caricamento esplicito di un'entità correlata.</span><span class="sxs-lookup"><span data-stu-id="b7940-165">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="b7940-166">I metodi delle voci restituiscono oggetti DbEntityEntry per molte o tutte le entità rilevate dal contesto.</span><span class="sxs-lookup"><span data-stu-id="b7940-166">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="b7940-167">In questo modo è possibile raccogliere informazioni o eseguire operazioni su molte entità anziché su una sola voce.</span><span class="sxs-lookup"><span data-stu-id="b7940-167">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="b7940-168">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7940-168">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

<span data-ttu-id="b7940-169">Si noterà che si sta introducendo una classe Author e Reader nell'esempio, entrambe le classi implementano l'interfaccia IPerson.</span><span class="sxs-lookup"><span data-stu-id="b7940-169">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

<span data-ttu-id="b7940-170">Si supponga che nel database siano presenti i dati seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7940-170">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="b7940-171">Blog con BlogId = 1 e Name =' ADO.NET Blog '</span><span class="sxs-lookup"><span data-stu-id="b7940-171">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="b7940-172">Blog con BlogId = 2 e nome = "Blog di Visual Studio"</span><span class="sxs-lookup"><span data-stu-id="b7940-172">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="b7940-173">Blog con BlogId = 3 e Name =' .NET Framework Blog '</span><span class="sxs-lookup"><span data-stu-id="b7940-173">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="b7940-174">Autore con autorizzazione = 1 e nome =' Joe Bloggs '</span><span class="sxs-lookup"><span data-stu-id="b7940-174">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="b7940-175">Reader con ReaderId = 1 e Name =' John Doe '</span><span class="sxs-lookup"><span data-stu-id="b7940-175">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="b7940-176">L'output dell'esecuzione del codice è:</span><span class="sxs-lookup"><span data-stu-id="b7940-176">The output from running the code would be:</span></span>  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

<span data-ttu-id="b7940-177">In questi esempi vengono illustrati diversi punti:</span><span class="sxs-lookup"><span data-stu-id="b7940-177">These examples illustrate several points:</span></span>  

- <span data-ttu-id="b7940-178">I metodi delle voci restituiscono le voci per le entità in tutti gli Stati, incluso eliminato.</span><span class="sxs-lookup"><span data-stu-id="b7940-178">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="b7940-179">Confrontare questa impostazione con local che esclude le entità eliminate.</span><span class="sxs-lookup"><span data-stu-id="b7940-179">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="b7940-180">Le voci per tutti i tipi di entità vengono restituite quando viene usato il metodo per le voci non generiche.</span><span class="sxs-lookup"><span data-stu-id="b7940-180">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="b7940-181">Quando viene utilizzato il metodo delle voci generiche, le voci vengono restituite solo per le entità che sono istanze del tipo generico.</span><span class="sxs-lookup"><span data-stu-id="b7940-181">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="b7940-182">Questa operazione è stata usata in precedenza per ottenere le voci per tutti i Blog.</span><span class="sxs-lookup"><span data-stu-id="b7940-182">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="b7940-183">È stata usata anche per ottenere le voci per tutte le entità che implementano IPerson.</span><span class="sxs-lookup"><span data-stu-id="b7940-183">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="b7940-184">Ciò dimostra che il tipo generico non deve essere un tipo di entità effettivo.</span><span class="sxs-lookup"><span data-stu-id="b7940-184">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="b7940-185">LINQ to Objects può essere usato per filtrare i risultati restituiti.</span><span class="sxs-lookup"><span data-stu-id="b7940-185">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="b7940-186">Questa operazione è stata usata in precedenza per trovare entità di qualsiasi tipo, purché vengano modificate.</span><span class="sxs-lookup"><span data-stu-id="b7940-186">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="b7940-187">Si noti che le istanze di DbEntityEntry contengono sempre un'entità non null.</span><span class="sxs-lookup"><span data-stu-id="b7940-187">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="b7940-188">Le voci di relazione e le voci stub non sono rappresentate come istanze di DbEntityEntry, pertanto non è necessario filtrare tali voci.</span><span class="sxs-lookup"><span data-stu-id="b7940-188">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
