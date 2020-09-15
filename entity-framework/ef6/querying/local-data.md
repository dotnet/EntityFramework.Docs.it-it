---
title: Dati locali-EF6
description: Dati locali in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: 5763424ab295ed6ceec655368fe3565a10c911d3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073912"
---
# <a name="local-data"></a>Dati locali
L'esecuzione di una query LINQ direttamente su un DbSet invierà sempre una query al database, ma è possibile accedere ai dati attualmente in memoria usando la proprietà DbSet. local. È anche possibile accedere alle informazioni aggiuntive EF tenendo traccia delle entità usando i metodi DbContext. entry e DbContext. ChangeTracker. entrys. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

## <a name="using-local-to-look-at-local-data"></a>Uso locale per esaminare i dati locali  

La proprietà locale di DbSet consente di accedere in modo semplice alle entità del set attualmente rilevate dal contesto e non sono state contrassegnate come eliminate. L'accesso alla proprietà locale non determina mai l'invio di una query al database. Ciò significa che viene in genere usato dopo che una query è già stata eseguita. Il metodo di estensione Load può essere utilizzato per eseguire una query in modo che il contesto rilevi i risultati. Esempio:  

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

Se nel database sono presenti due Blog: "ADO.NET Blog" con BlogId 1 e "The Visual Studio Blog" con BlogId 2, si potrebbe prevedere l'output seguente:  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

Vengono illustrati tre punti:  

- Il nuovo Blog "My New Blog" è incluso nella raccolta locale anche se non è ancora stato salvato nel database. Questo Blog presenta una chiave primaria pari a zero perché il database non ha ancora generato una chiave reale per l'entità.  
- Il Blog ' ADO.NET ' non è incluso nella raccolta locale anche se è ancora rilevato dal contesto. Questo è dovuto al fatto che è stato rimosso dal DbSet, in modo da contrassegnarlo come eliminato.  
- Quando si usa DbSet per eseguire una query, il Blog contrassegnato per l'eliminazione (Blog di ADO.NET) è incluso nei risultati e il nuovo Blog (nuovo Blog) che non è ancora stato salvato nel database non è incluso nei risultati. Questo perché DbSet esegue una query sul database e i risultati restituiti riflettono sempre il contenuto del database.  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>Uso di local per aggiungere e rimuovere entità dal contesto  

La proprietà locale in DbSet restituisce un oggetto [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) con eventi collegati in modo che rimanga sincronizzato con il contenuto del contesto. Questo significa che le entità possono essere aggiunte o rimosse dalla raccolta locale o da DbSet. Significa anche che le query che portano nuove entità nel contesto comporteranno l'aggiornamento della raccolta locale con tali entità. Esempio:  

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

Supponendo che siano stati contrassegnati alcuni post con ' Entity-Framework ' è asp.net ', l'output potrebbe essere simile al seguente:  

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

Vengono illustrati tre punti:  

- Il nuovo post ' What ' s New in EF ' aggiunto alla raccolta locale viene rilevato dal contesto nello stato Added. Che verrà quindi inserita nel database quando viene chiamato SaveChanges.  
- Il post rimosso dalla raccolta locale (Guida per principianti di EF) è ora contrassegnato come eliminato nel contesto. Verrà quindi eliminato dal database quando viene chiamato SaveChanges.  
- Il post aggiuntivo (Guida per principianti ASP.NET) caricato nel contesto con la seconda query viene aggiunto automaticamente alla raccolta locale.  

Un aspetto finale da considerare per quanto riguarda local è il fatto che, poiché si tratta di una performance ObservableCollection, non è ideale per un numero elevato di entità. Se pertanto si utilizzano migliaia di entità nel contesto, potrebbe non essere consigliabile utilizzare local.  

## <a name="using-local-for-wpf-data-binding"></a>Uso di local per WPF data binding  

La proprietà locale in DbSet può essere utilizzata direttamente per data binding in un'applicazione WPF perché è un'istanza di ObservableCollection. Come descritto nelle sezioni precedenti, questo significa che verrà automaticamente sincronizzato con il contenuto del contesto e il contenuto del contesto resterà sincronizzato automaticamente. Si noti che è necessario pre-popolare la raccolta locale con i dati in modo che sia necessario eseguire il binding perché local non causa mai una query sul database.  

Non si tratta di una posizione appropriata per un esempio di data binding WPF completo, ma gli elementi chiave sono:  

- Configurare un'origine di binding  
- Associarlo alla proprietà locale del set  
- Popolare local utilizzando una query nel database.  

## <a name="wpf-binding-to-navigation-properties"></a>Associazione WPF alle proprietà di navigazione  

Se si sta eseguendo data binding Master/Detail, è possibile associare la visualizzazione dettagli a una proprietà di navigazione di una delle entità. Un modo semplice per eseguire questa operazione consiste nell'usare un oggetto ObservableCollection per la proprietà di navigazione. Esempio:  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>Uso di local per la pulizia delle entità in SaveChanges  

Nella maggior parte dei casi le entità rimosse da una proprietà di navigazione non verranno contrassegnate automaticamente come eliminate nel contesto. Se ad esempio si rimuove un oggetto post dalla raccolta Blog. Posts, il post non verrà eliminato automaticamente quando viene chiamato SaveChanges. Se è necessario eliminarlo, potrebbe essere necessario trovare queste entità penzoloni e contrassegnarle come eliminate prima di chiamare SaveChanges o come parte di un oggetto SaveChanges sottoposto a override. Esempio:  

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

Il codice precedente usa la raccolta locale per trovare tutti i post e contrassegna quelli che non dispongono di un riferimento a Blog come eliminati. La chiamata ToList è obbligatoria perché, in caso contrario, la raccolta verrà modificata dalla chiamata Remove mentre è in corso l'enumerazione. Nella maggior parte delle situazioni è possibile eseguire una query direttamente sulla proprietà locale senza prima usare ToList.  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>Utilizzo di local e tobinding per Windows Forms data binding  

Windows Forms non supporta la fedeltà completa data binding utilizzando direttamente ObservableCollection. Tuttavia, è comunque possibile usare la proprietà locale DbSet per data binding per ottenere tutti i vantaggi descritti nelle sezioni precedenti. Questa operazione viene eseguita tramite il metodo di estensione tobinding, che consente di creare un'implementazione di [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) supportata dalla classe ObservableCollection locale.  

Non si tratta di una posizione appropriata per un Windows Forms completo data binding esempio, ma gli elementi chiave sono:  

- Configurare un'origine di associazione di oggetti  
- Associarlo alla proprietà locale del set usando local. tobinding ()  
- Popolare local usando una query nel database  

## <a name="getting-detailed-information-about-tracked-entities"></a>Ottenere informazioni dettagliate sulle entità rilevate  

Molti degli esempi di questa serie usano il metodo entry per restituire un'istanza di DbEntityEntry per un'entità. Questo oggetto entry funge quindi da punto di partenza per la raccolta di informazioni sull'entità, ad esempio lo stato corrente, nonché per l'esecuzione di operazioni sull'entità, ad esempio il caricamento esplicito di un'entità correlata.  

I metodi delle voci restituiscono oggetti DbEntityEntry per molte o tutte le entità rilevate dal contesto. In questo modo è possibile raccogliere informazioni o eseguire operazioni su molte entità anziché su una sola voce. Esempio:  

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

Si noterà che si sta introducendo una classe Author e Reader nell'esempio, entrambe le classi implementano l'interfaccia IPerson.  

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

Si supponga che nel database siano presenti i dati seguenti:

Blog con BlogId = 1 e Name =' ADO.NET Blog '  
Blog con BlogId = 2 e nome = "Blog di Visual Studio"  
Blog con BlogId = 3 e Name =' .NET Framework Blog '  
Autore con autorizzazione = 1 e nome =' Joe Bloggs '  
Reader con ReaderId = 1 e Name =' John Doe '  

L'output dell'esecuzione del codice è:  

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

In questi esempi vengono illustrati diversi punti:  

- I metodi delle voci restituiscono le voci per le entità in tutti gli Stati, incluso eliminato. Confrontare questa impostazione con local che esclude le entità eliminate.  
- Le voci per tutti i tipi di entità vengono restituite quando viene usato il metodo per le voci non generiche. Quando viene utilizzato il metodo delle voci generiche, le voci vengono restituite solo per le entità che sono istanze del tipo generico. Questa operazione è stata usata in precedenza per ottenere le voci per tutti i Blog. È stata usata anche per ottenere le voci per tutte le entità che implementano IPerson. Ciò dimostra che il tipo generico non deve essere un tipo di entità effettivo.  
- LINQ to Objects può essere usato per filtrare i risultati restituiti. Questa operazione è stata usata in precedenza per trovare entità di qualsiasi tipo, purché vengano modificate.  

Si noti che le istanze di DbEntityEntry contengono sempre un'entità non null. Le voci di relazione e le voci stub non sono rappresentate come istanze di DbEntityEntry, pertanto non è necessario filtrare tali voci.
