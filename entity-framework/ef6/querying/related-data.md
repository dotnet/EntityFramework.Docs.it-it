---
title: Caricamento di entità correlate-EF6
description: Caricamento di entità correlate in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/related-data
ms.openlocfilehash: d1bf04f9d9017291ef3f5ec0809095df856329cd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073873"
---
# <a name="loading-related-entities"></a>Caricamento di entità correlate

Entity Framework supporta tre modi per caricare dati correlati, caricamento lazy e caricamento esplicito. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.

## <a name="eagerly-loading"></a>Caricamento eager

Il caricamento eager è il processo in base al quale una query per un tipo di entità carica anche le entità correlate come parte della query. Il caricamento eager viene effettuato tramite il metodo include. Ad esempio, le query seguenti caricherà i Blog e tutti i post correlati a ogni blog.

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include è un metodo di estensione nello spazio dei nomi System. Data. Entity, quindi assicurarsi di usare tale spazio dei nomi.

### <a name="eagerly-loading-multiple-levels"></a>Caricamento eager di più livelli

È anche possibile caricare con entusiasmo più livelli di entità correlate. Nelle query riportate di seguito vengono illustrati esempi di come eseguire questa operazione sia per le proprietà di navigazione di raccolta che di riferimento  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> Non è attualmente possibile filtrare le entità correlate caricate. Includi porta sempre in tutte le entità correlate.  

## <a name="lazy-loading"></a>Caricamento lazy

Il caricamento lazy è il processo in base al quale un'entità o una raccolta di entità viene caricata automaticamente dal database la prima volta che si accede a una proprietà che fa riferimento all'entità o alle entità. Quando si usano i tipi di entità POCO, il caricamento lazy viene ottenuto creando istanze di tipi proxy derivati e quindi eseguendo l'override delle proprietà virtuali per aggiungere l'hook di caricamento. Ad esempio, quando si usa la classe di entità Blog definita di seguito, i post correlati verranno caricati la prima volta che si accede alla proprietà di navigazione post:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>Disabilitare il caricamento lazy per la serializzazione

Il caricamento lazy e la serializzazione non sono combinati correttamente e, se non si presta attenzione, è possibile eseguire query per l'intero database solo perché il caricamento lazy è abilitato. La maggior parte dei serializzatori funziona accedendo a ogni proprietà in un'istanza di un tipo. L'accesso alle proprietà attiva il caricamento lazy, in modo che vengano serializzate più entità. È possibile accedere alle proprietà delle entità e vengono caricate anche altre entità. È consigliabile disattivare il caricamento lazy prima di serializzare un'entità. Le sezioni seguenti mostrano come eseguire questa operazione.

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>Disattivazione del caricamento lazy per specifiche proprietà di navigazione

Il caricamento lazy della raccolta post può essere disattivato rendendo la proprietà post non virtuale:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

Il caricamento della raccolta dei post può ancora essere eseguito usando il caricamento eager (vedere *caricamento eager* sopra) o il metodo Load (vedere *caricamento esplicito* di seguito).

### <a name="turn-off-lazy-loading-for-all-entities"></a>Disattiva caricamento lazy per tutte le entità

Il caricamento lazy può essere disattivato per tutte le entità nel contesto impostando un flag sulla proprietà di configurazione. Esempio:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

Il caricamento di entità correlate può comunque essere eseguito usando il caricamento eager (vedere *caricamento eager* sopra) o il metodo Load (vedere *caricamento esplicito* di seguito).

## <a name="explicitly-loading"></a>Caricamento esplicito

Anche con il caricamento lazy disabilitato, è comunque possibile caricare in modo differito le entità correlate, ma è necessario eseguire una chiamata esplicita. A tale scopo, utilizzare il metodo Load sulla voce dell'entità correlata. Esempio:

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> Il metodo di riferimento deve essere utilizzato quando un'entità dispone di una proprietà di navigazione per un'altra entità singola. D'altra parte, il metodo di raccolta deve essere usato quando un'entità dispone di una proprietà di navigazione per una raccolta di altre entità.

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>Applicazione di filtri quando si caricano in modo esplicito le entità correlate

Il metodo di query consente di accedere alla query sottostante che Entity Framework utilizzerà per il caricamento di entità correlate. È quindi possibile usare LINQ per applicare filtri alla query prima di eseguirla con una chiamata a un metodo di estensione LINQ, ad esempio ToList, Load e così via. Il metodo di query può essere utilizzato sia con proprietà di navigazione di riferimento che di raccolta, ma è particolarmente utile per le raccolte in cui può essere utilizzato per caricare solo parte della raccolta. Esempio:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

Quando si usa il metodo di query, in genere è preferibile disattivare il caricamento lazy per la proprietà di navigazione. Questo perché, in caso contrario, l'intera raccolta potrebbe essere caricata automaticamente dal meccanismo di caricamento lazy prima o dopo l'esecuzione della query filtrata.

> [!NOTE]
> Sebbene la relazione possa essere specificata come stringa anziché come espressione lambda, l'oggetto IQueryable restituito non è generico quando viene utilizzata una stringa e quindi il metodo Cast è in genere necessario prima che sia possibile eseguire operazioni utili.

## <a name="using-query-to-count-related-entities-without-loading-them"></a>Utilizzo di query per conteggiare entità correlate senza caricarle

A volte è utile sapere quante entità sono correlate a un'altra entità nel database senza dover effettivamente sostenere il costo del caricamento di tutte le entità. Per eseguire questa operazione, è possibile usare il metodo di query con il metodo di conteggio LINQ. Ad esempio:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
