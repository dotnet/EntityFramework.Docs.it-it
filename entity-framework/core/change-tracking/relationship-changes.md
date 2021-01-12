---
title: Modifica di chiavi esterne e spostamenti-EF Core
description: Come modificare le relazioni tra le entità modificando le chiavi esterne e le navigazioni
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: ac2110509b6748e85411dbb14989522465925ecf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129743"
---
# <a name="changing-foreign-keys-and-navigations"></a>Modifica di chiavi esterne e spostamenti

## <a name="overview-of-foreign-keys-and-navigations"></a>Panoramica delle chiavi esterne e delle navigazioni

Le relazioni in un modello di Entity Framework Core (EF Core) sono rappresentate mediante chiavi esterne (FKs). Un FK è costituito da una o più proprietà nell'entità dipendente o figlio nella relazione. Questa entità dipendente/figlio è associata a un'entità principale/padre specificata quando i valori delle proprietà di chiave esterna nell'oggetto dipendente/figlio corrispondono ai valori delle proprietà di chiave alternativa o primaria (PK) nell'entità/padre.

Le chiavi esterne sono un metodo efficace per archiviare e modificare le relazioni nel database, ma non sono molto intuitive quando si utilizzano più entità correlate nel codice dell'applicazione. Pertanto, la maggior parte dei modelli di EF Core anche livelli di "navigazione" sulla rappresentazione FK. Gli spostamenti formano riferimenti C#/.NET tra istanze di entità che riflettono le associazioni individuate mediante la corrispondenza dei valori di chiave esterna con valori di chiave primari o alternativi.

Le esplorazioni possono essere utilizzate su entrambi i lati della relazione, solo su un lato, lasciando solo la proprietà FK. La proprietà FK può essere nascosta rendendola una [proprietà shadow](xref:core/modeling/shadow-properties). Per ulteriori informazioni sulle relazioni di modellazione, vedere [relazioni](xref:core/modeling/relationships) .

> [!TIP]
> In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi. Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).

### <a name="example-model"></a>Modello di esempio

Il modello seguente contiene quattro tipi di entità con relazioni tra di essi. I commenti nel codice indicano quali proprietà sono chiavi esterne, chiavi primarie e spostamenti.

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

Le tre relazioni in questo modello sono:

- Ogni blog può avere molti post (uno-a-molti):
  - `Blog` è il principale/padre.
  - `Post` è l'oggetto dipendente/figlio. Contiene la proprietà FK `Post.BlogId` , il cui valore deve corrispondere al `Blog.Id` valore PK del blog correlato.
  - `Post.Blog` è una navigazione di riferimento da un post al Blog associato. `Post.Blog` è la navigazione inversa per `Blog.Posts` .
  - `Blog.Posts` è una navigazione di raccolta da un Blog a tutti i post associati. `Blog.Posts` è la navigazione inversa per `Post.Blog` .
- Ogni blog può avere un solo asset (uno-a-uno):
  - `Blog` è il principale/padre.
  - `BlogAssets` è l'oggetto dipendente/figlio. Contiene la proprietà FK `BlogAssets.BlogId` , il cui valore deve corrispondere al `Blog.Id` valore PK del blog correlato.
  - `BlogAssets.Blog` è una navigazione di riferimento dagli asset al Blog associato. `BlogAssets.Blog` è la navigazione inversa per `Blog.Assets` .
  - `Blog.Assets` è una navigazione di riferimento dal Blog agli asset associati. `Blog.Assets` è la navigazione inversa per `BlogAssets.Blog` .
- Ogni post può avere molti tag e ogni tag può avere molti post (many-to-many):
  - Le relazioni many-to-many sono un ulteriore livello rispetto alle relazioni 2 1-to-many. Le relazioni molti-a-molti sono descritte più avanti in questo documento.
  - `Post.Tags` è una navigazione della raccolta da un post a tutti i tag associati. `Post.Tags` è la navigazione inversa per `Tag.Posts` .
  - `Tag.Posts` è una navigazione della raccolta da un tag a tutti i post associati. `Tag.Posts` è la navigazione inversa per `Post.Tags` .

Per ulteriori informazioni sulla modellazione e la configurazione delle relazioni, vedere [relazioni](xref:core/modeling/relationships) .

## <a name="relationship-fixup"></a>Correzione di relazioni

EF Core mantiene le navigazioni in allineamento con i valori di chiave esterna e viceversa. Ovvero, se un valore di chiave esterna viene modificato in modo da fare riferimento a un'entità principale/padre diversa, le navigazioni vengono aggiornate in base a questa modifica. Analogamente, se viene modificata una navigazione, i valori di chiave esterna delle entità interessati vengono aggiornati per riflettere questa modifica. Questo metodo è denominato "correzione della relazione".

### <a name="fixup-by-query"></a>Correzione per query

La correzione viene eseguita per prima quando viene eseguita una query sulle entità dal database. Il database dispone solo di valori di chiave esterna, pertanto quando EF Core crea un'istanza di entità dal database usa i valori di chiave esterna per impostare le esplorazioni dei riferimenti e aggiungere entità alle spostamenti della raccolta in base alle esigenze. Si consideri, ad esempio, una query per i Blog e i post e gli asset associati:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

Per ogni Blog, EF Core creerà prima un' `Blog` istanza. Quindi, quando ogni post viene caricato dal database, la relativa `Post.Blog` navigazione di riferimento viene impostata in modo da puntare al Blog associato. Analogamente, il post viene aggiunto alla `Blog.Posts` navigazione della raccolta. Lo stesso accade con `BlogAssets` , ad eccezione del fatto che entrambi gli spostamenti sono riferimenti. La `Blog.Assets` navigazione viene impostata in modo da puntare all'istanza di asset e la `BlogAsserts.Blog` navigazione viene impostata in modo da puntare all'istanza del Blog.

Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa query vengono visualizzati due Blog, ognuno con un asset e due post rilevati:

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

La visualizzazione debug Mostra sia i valori chiave che gli spostamenti. Le navigazioni vengono visualizzate utilizzando i valori di chiave primaria delle entità correlate. Nell'output precedente, ad esempio, viene `Posts: [{Id: 1}, {Id: 2}]` indicato che la `Blog.Posts` navigazione della raccolta contiene due post correlati rispettivamente con chiavi primarie 1 e 2. Analogamente, per ogni post associato al primo Blog, la `Blog: {Id: 1}` riga indica che la `Post.Blog` navigazione fa riferimento al Blog con chiave primaria 1.

### <a name="fixup-to-locally-tracked-entities"></a>Correzione delle entità rilevate localmente

La correzione delle relazioni si verifica anche tra le entità restituite da una query di rilevamento e le entità già rilevate da DbContext. Si consideri, ad esempio, l'esecuzione di tre query separate per Blog, post e asset:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[! code-CSharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Una volta esaminate le visualizzazioni di debug, dopo la prima query vengono rilevati solo i due Blog:

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

Le esplorazioni dei `Blog.Assets` riferimenti sono null e le esplorazioni della `Blog.Posts` raccolta sono vuote perché nessuna entità associata è attualmente rilevata dal contesto.

Dopo la seconda query, le esplorazioni dei `Blogs.Assets` riferimenti sono state corrette per puntare alle nuove istanze rilevate `BlogAsset` . Analogamente, le esplorazioni dei `BlogAssets.Blog` riferimenti sono impostate in modo da puntare all'istanza già rilevata appropriata `Blog` .

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

Infine, dopo la terza query, le esplorazioni della `Blog.Posts` raccolta ora contengono tutti i post correlati e i `Post.Blog` riferimenti puntano all' `Blog` istanza appropriata:

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

Si tratta dello stesso stato finale come è stato fatto con la singola query originale, dal momento che EF Core ha risolto le spostamenti durante il rilevamento delle entità, anche quando provengono da più query diverse.

> [!NOTE]
> La correzione non comporta mai la restituzione di più dati dal database. Connette solo le entità già restituite dalla query o già rilevate da DbContext. Per informazioni sulla gestione dei duplicati durante la serializzazione di entità, vedere [risoluzione delle identità in EF Core](xref:core/change-tracking/identity-resolution) .

## <a name="changing-relationships-using-navigations"></a>Modifica delle relazioni mediante le navigazioni

Il modo più semplice per modificare la relazione tra due entità consiste nel manipolare una navigazione, lasciando EF Core per correggere i valori di navigazione inversa e FK in modo appropriato. Puoi farlo in due modi:

- Aggiunta o rimozione di un'entità da una navigazione della raccolta.
- Modifica di una navigazione di riferimento in modo che punti a un'entità diversa o impostandola su null.

### <a name="adding-or-removing-from-collection-navigations"></a>Aggiunta o rimozione dall'esplorazione della raccolta

Si sposti, ad esempio, uno dei post del Blog di Visual Studio nel Blog di .NET. Questa operazione richiede prima di tutto il caricamento dei Blog e dei post e quindi lo spostamento del post dalla raccolta di navigazione in un Blog alla raccolta di navigazione nell'altro Blog:

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
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Qui è necessaria una chiamata a perché l'accesso alla visualizzazione di debug non causa il [rilevamento automatico delle modifiche](xref:core/change-tracking/change-detection).

Questa è la visualizzazione di debug stampata dopo l'esecuzione del codice precedente:

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

`Blog.Posts`Per la navigazione nel Blog di .NET sono ora presenti tre post ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ). Analogamente, la `Blog.Posts` navigazione nel Blog di Visual Studio ha solo un post ( `Posts: [{Id: 4}]` ). Questa operazione è prevista perché il codice ha modificato in modo esplicito queste raccolte.

Più interessante, anche se il codice non ha modificato in modo esplicito la `Post.Blog` navigazione, è stato corretto per puntare al Blog di Visual Studio ( `Blog: {Id: 1}` ). Inoltre, il `Post.BlogId` valore della chiave esterna è stato aggiornato in modo che corrisponda al valore di chiave primaria del Blog .NET. Questa modifica apportata al valore FK in viene salvata in modo permanente nel database quando viene chiamato SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>Modifica delle navigazione di riferimento

Nell'esempio precedente, un post è stato spostato da un Blog a un altro modificando la navigazione della raccolta dei post in ogni blog. La stessa operazione può essere eseguita modificando invece la `Post.Blog` navigazione di riferimento in modo che punti al nuovo blog. Ad esempio:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

La visualizzazione di debug dopo questa modifica _è identica a quella dell'_ esempio precedente. Questo perché EF Core ha rilevato la modifica della navigazione di riferimento e quindi ha risolto le spostamenti della raccolta e il valore FK in modo che corrispondano.

## <a name="changing-relationships-using-foreign-key-values"></a>Modifica di relazioni con valori di chiave esterna

Nella sezione precedente, le relazioni sono state modificate dalle navigazioni che lasciano i valori di chiave esterna per essere aggiornati automaticamente. Si tratta della modalità consigliata per modificare le relazioni in EF Core. Tuttavia, è anche possibile modificare direttamente i valori FK. Ad esempio, è possibile spostare un post da un Blog a un altro modificando il `Post.BlogId` valore della chiave esterna:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

Si noti che questa operazione è molto simile alla modifica della navigazione dei riferimenti, come illustrato nell'esempio precedente.

La visualizzazione di debug dopo questa modifica è _identica a_ quella del caso dei due esempi precedenti. Questo perché EF Core ha rilevato la modifica del valore FK, quindi è stato corretto il riferimento e le navigazioni della raccolta in modo che corrispondano.

> [!TIP]
> Non scrivere codice per modificare tutti gli spostamenti e i valori FK ogni volta che una relazione viene modificata. Tale codice è più complesso e deve garantire modifiche coerenti alle chiavi esterne e agli spostamenti in ogni caso. Se possibile, è sufficiente modificare una sola navigazione oppure entrambe le navigazioni. Se necessario, è sufficiente modificare i valori FK. Evitare di modificare sia gli spostamenti che i valori FK.

## <a name="fixup-for-added-or-deleted-entities"></a>Correzione per entità aggiunte o eliminate

### <a name="adding-to-a-collection-navigation"></a>Aggiunta a una navigazione della raccolta

EF Core esegue le azioni seguenti quando [rileva](xref:core/change-tracking/change-detection) che è stata aggiunta una nuova entità dipendente/figlio a una navigazione della raccolta:

- Se l'entità non viene rilevata, viene rilevata. (L'entità si trova in genere nello `Added` stato. Tuttavia, se il tipo di entità è configurato per usare le chiavi generate e il valore della chiave primaria è impostato, l'entità viene rilevata nello `Unchanged` stato.
- Se l'entità è associata a un'entità o a un elemento padre diverso, la relazione è grave.
- L'entità viene associata al principale/padre che possiede la navigazione della raccolta.
- Gli spostamenti e i valori di chiave esterna sono corretti per tutte le entità di cui è stato richiesto.

In base a questo, possiamo notare che per spostare un post da un Blog a un altro non è necessario rimuoverlo dalla precedente navigazione della raccolta prima di aggiungerlo al nuovo. Il codice dell'esempio precedente può quindi essere modificato da:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

Con:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core rileva che il post è stato aggiunto a un nuovo blog e lo rimuove automaticamente dalla raccolta nel primo Blog.

### <a name="removing-from-a-collection-navigation"></a>Rimozione da una navigazione della raccolta

La rimozione di un'entità dipendente/figlio dall'esplorazione della raccolta del principale/padre causa il troncamento della relazione a tale entità/elemento padre. Ciò che accade dopo dipende dal fatto che la relazione sia facoltativa o richiesta.

#### <a name="optional-relationships"></a>Relazioni facoltative

Per impostazione predefinita, per le relazioni facoltative, il valore della chiave esterna è impostato su null. Ciò significa che il dipendente/figlio non è più associato ad _alcuna_ entità/elemento padre. Ad esempio, caricare un blog e post, quindi rimuovere uno dei post dall' `Blog.Posts` esplorazione della raccolta:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa modifica, viene visualizzato quanto segue:

- `Post.BlogId`FK è stato impostato su null ( `BlogId: <null> FK Modified Originally 1` )
- La `Post.Blog` navigazione di riferimento è stata impostata su null ( `Blog: <null>` )
- Il post è stato rimosso dalla `Blog.Posts` navigazione della raccolta ( `Posts: [{Id: 1}]` )

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

Si noti che il post _non_ è contrassegnato come `Deleted` . È contrassegnato come in `Modified` modo che il valore FK nel database venga impostato su null quando viene chiamato SaveChanges.

#### <a name="required-relationships"></a>Relazioni obbligatorie

L'impostazione del valore FK su null non è consentita (e in genere non è possibile) per le relazioni obbligatorie. Pertanto, il troncamento di una relazione obbligatoria significa che l'entità dipendente/figlio deve avere un nuovo elemento padre in una nuova entità/padre oppure essere rimossa dal database quando viene chiamato SaveChanges per evitare la violazione di un vincolo referenziale. Questa operazione è nota come "eliminazione di orfani" ed è il comportamento predefinito in EF Core per le relazioni obbligatorie.

È ad esempio possibile modificare la relazione tra Blog e post in modo che sia necessario, quindi eseguire lo stesso codice dell'esempio precedente:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

Esaminando la visualizzazione di debug dopo questa modifica, viene visualizzato quanto segue:

- Il post è stato contrassegnato in modo tale `Deleted` che verrà eliminato dal database quando viene chiamato SaveChanges.
- La `Post.Blog` navigazione di riferimento è stata impostata su null ( `Blog: <null>` ).
- Il post è stato rimosso dalla `Blog.Posts` navigazione della raccolta ( `Posts: [{Id: 1}]` ).

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

Si noti che `Post.BlogId` rimane invariato perché per una relazione obbligatoria non può essere impostato su null.

La chiamata a SaveChanges comporta l'eliminazione del post orfano:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>Elimina la tempistica degli orfani e il nuovo elemento padre

Per impostazione predefinita, contrassegnare gli orfani come avviene non appena `Deleted` viene [rilevata](xref:core/change-tracking/change-detection)la modifica della relazione. Tuttavia, questo processo può essere posticipato fino a quando non viene effettivamente chiamato SaveChanges. Questa operazione può essere utile per evitare di rendere orfani le entità che sono state rimosse da un'entità/elemento padre, ma verrà riassociato un nuovo elemento padre con una nuova entità/padre prima della chiamata a SaveChanges. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> viene usato per impostare questa temporizzazione. Ad esempio:

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

Dopo la rimozione del post dalla prima raccolta, l'oggetto non è contrassegnato come `Deleted` l'esempio precedente. EF Core verifica invece che la relazione venga interrotta _anche se si tratta di una relazione obbligatoria_. Il valore FK viene considerato null da EF Core anche se non può essere effettivamente null perché il tipo non ammette i valori null. Questa operazione è nota come "null concettuale".

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

La chiamata a SaveChanges in questo momento comporta l'eliminazione del post orfano. Tuttavia, se come nell'esempio precedente, post è associato a un nuovo blog prima della chiamata a SaveChanges, il post verrà corretto in modo appropriato per il nuovo blog e non sarà più considerato orfano:

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

Il metodo SaveChanges chiamato a questo punto aggiornerà il post nel database anziché eliminarlo.

È anche possibile disattivare l'eliminazione automatica degli orfani. Questa operazione genererà un'eccezione se viene chiamato SaveChanges mentre è in corso il rilevamento di un orfano. Ad esempio, il codice seguente:

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

Genererà questa eccezione:

> System. InvalidOperationException: l'associazione tra le entità' Blog ' è post ' con il valore di chiave ' {BlogId: 1}' è stata interrotta, ma la relazione è contrassegnata come obbligatoria o è richiesta in modo implicito perché la chiave esterna non ammette i valori null. Se l'entità dipendente/figlio deve essere eliminata quando una relazione obbligatoria è grave, configurare la relazione per l'utilizzo delle eliminazioni a catena.

L'eliminazione di orfani, nonché di eliminazioni a cascata, può essere forzata in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . Se si combina questa operazione con l'impostazione dell'intervallo di eliminazione orfano `Never` , gli orfani non verranno mai eliminati, a meno che non venga esplicitamente richiesto EF core.

### <a name="changing-a-reference-navigation"></a>Modifica di una navigazione di riferimento

La modifica della navigazione di riferimento di una relazione uno-a-molti ha lo stesso effetto della modifica della navigazione della raccolta nell'altra entità finale della relazione. L'impostazione della navigazione di riferimento di dipendente/figlio su null equivale a rimuovere l'entità dalla navigazione della raccolta del principale/padre. Tutte le correzioni e le modifiche al database vengono apportate come descritto nella sezione precedente, inclusa la creazione di un'entità orfana se la relazione è obbligatoria.

#### <a name="optional-one-to-one-relationships"></a>Relazioni uno-a-uno facoltative

Per le relazioni uno-a-uno, la modifica di una navigazione di riferimento causa la reimpostazione di qualsiasi relazione precedente. Per le relazioni facoltative, questo significa che il valore FK sul dipendente/figlio correlato in precedenza è impostato su null. Ad esempio:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

La visualizzazione debug prima di chiamare SaveChanges Mostra che i nuovi asset hanno sostituito gli asset esistenti, che ora è contrassegnato come `Modified` con un `BlogAssets.BlogId` valore FK null:

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

Ciò comporta un aggiornamento e un inserimento quando viene chiamato SaveChanges:

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

#### <a name="required-one-to-one-relationships"></a>Relazioni uno-a-uno obbligatorie

Eseguendo lo stesso codice dell'esempio precedente, ma questa volta con una relazione uno-a-uno obbligatoria, Mostra che l'oggetto associato in precedenza `BlogAssets` è ora contrassegnato come `Deleted` , poiché diventa un orfano quando il nuovo `BlogAssets` prende il posto:

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

Viene quindi generata un'istruzione Delete an e Insert quando SaveChanges viene chiamato:

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

I tempi di contrassegno degli orfani come eliminati possono essere modificati nello stesso modo in cui vengono visualizzati per le esplorazioni delle raccolte e hanno gli stessi effetti.

### <a name="deleting-an-entity"></a>Eliminazione di un'entità

#### <a name="optional-relationships"></a>Relazioni facoltative

Quando un'entità è contrassegnata come `Deleted` , ad esempio chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , i riferimenti all'entità eliminata vengono rimossi dagli spostamenti di altre entità. Per le relazioni facoltative, i valori FK in entità dipendenti vengono impostati su null.

Ad esempio, è possibile contrassegnare il Blog di Visual Studio come `Deleted` :

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

Esaminare la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges Mostra:

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

Si noti che:

- Il Blog è contrassegnato come `Deleted` .
- Gli asset correlati al Blog eliminato hanno un valore FK null ( `BlogId: <null> FK Modified Originally 2` ) e una navigazione di riferimento null ( `Blog: <null>` )
- Ogni post correlato al Blog eliminato ha un valore FK null ( `BlogId: <null> FK Modified Originally 2` ) e una navigazione di riferimento null ( `Blog: <null>` )

#### <a name="required-relationships"></a>Relazioni obbligatorie

Il comportamento della correzione per le relazioni obbligatorie è identico a quello delle relazioni facoltative, ad eccezione del fatto che le entità dipendenti/figlio sono contrassegnate come `Deleted` poiché non possono esistere senza un'entità/elemento padre e devono essere rimosse dal database quando viene chiamato SaveChanges per evitare un'eccezione di vincolo referenziale. Questa operazione è nota come "eliminazione a catena" ed è il comportamento predefinito in EF Core per le relazioni obbligatorie. Se, ad esempio, si esegue lo stesso codice dell'esempio precedente, ma con una relazione obbligatoria si ottiene la seguente visualizzazione di debug prima della chiamata a SaveChanges:

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

Come previsto, i dipendenti/figli sono ora contrassegnati come `Deleted` . Si noti tuttavia che le esplorazioni sulle entità eliminate _non_ sono state modificate. Questo può sembrare strano, ma evita di suddividere completamente un grafo eliminato di entità, cancellando tutte le navigazioni. Ovvero il Blog, l'asset e i post formano ancora un grafico delle entità anche dopo essere stati eliminati. In questo modo è molto più semplice annullare l'eliminazione di un grafico di entità rispetto al caso in EF6 in cui il grafo è stato triturato.

#### <a name="cascade-delete-timing-and-re-parenting"></a>Intervallo di eliminazione a catena e nuovo elemento padre

Per impostazione predefinita, l'eliminazione a catena viene eseguita non appena l'elemento padre/principale è contrassegnato come `Deleted` . Questa operazione è identica a quella per l'eliminazione di orfani, come descritto in precedenza. Come per l'eliminazione di orfani, questo processo può essere posticipato fino a quando non viene chiamato SaveChanges o anche completamente disabilitato, impostando in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> modo appropriato. Questa operazione è utile in modo analogo a quanto avviene per l'eliminazione di elementi orfani, inclusa la reassociazione di elementi figlio/dipendenti dopo l'eliminazione di un'entità o di un elemento padre.

Le eliminazioni a catena, oltre all'eliminazione di orfani, possono essere forzate in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . Se si combina questa impostazione con l'impostazione dell'intervallo di eliminazione a cascata `Never` , le eliminazioni a catena verranno eseguite solo se a EF core viene esplicitamente richiesto di eseguire questa operazione.

> [!TIP]
> Gli orfani a catena e eliminazione sono strettamente correlati. Entrambe comportano l'eliminazione di entità dipendenti/figlio quando la relazione con l'entità/padre richiesta è grave. Per l'eliminazione a catena, questo errore si verifica perché il server principale/padre viene eliminato. Per gli orfani, l'entità principale/padre continua a esistere, ma non è più correlata alle entità dipendenti/figlio.

## <a name="many-to-many-relationships"></a>Relazioni molti-a-molti

Le relazioni molti-a-molti in EF Core vengono implementate mediante un'entità di join. Ogni lato la relazione molti-a-molti è correlato a questa entità di join con una relazione uno-a-molti. Prima di EF Core 5,0, questa entità di join doveva essere definita e mappata in modo esplicito. A partire da EF Core 5,0, può essere creato in modo implicito e nascosto. In entrambi i casi, tuttavia, il comportamento sottostante è lo stesso. Questo comportamento sottostante verrà innanzitutto esaminato per comprendere il funzionamento del rilevamento delle relazioni molti-a-molti.

### <a name="how-many-to-many-relationships-work"></a>Funzionamento delle relazioni molti-a-molti

Si consideri questo modello EF Core che crea una relazione molti-a-molti tra post e tag usando un tipo di entità join definito in modo esplicito:

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

Si noti che il `PostTag` tipo di entità join contiene due proprietà di chiave esterna. In questo modello, affinché un post sia correlato a un tag, deve essere presente un'entità di join PostTag in cui il `PostTag.PostId` valore della chiave esterna corrisponde al valore della chiave `Post.Id` primaria e dove il valore della `PostTag.TagId` chiave esterna corrisponde al valore della chiave `Tag.Id` primaria. Ad esempio:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo l'esecuzione di questo codice, viene mostrato che il post e il tag sono correlati dalla nuova `PostTag` entità join:

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

Si noti che gli spostamenti della raccolta in `Post` e `Tag` sono stati corretti, così come hanno le esplorazioni dei riferimenti in `PostTag` . Queste relazioni possono essere modificate tramite le navigazioni anziché i valori FK, come in tutti gli esempi precedenti. Il codice precedente, ad esempio, può essere modificato per aggiungere la relazione impostando le esplorazioni dei riferimenti nell'entità di join:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

Ciò comporta esattamente la stessa modifica a FKs e alle esplorazioni come nell'esempio precedente.

### <a name="skip-navigations"></a>Ignora navigazione

> [!NOTE]
> Le esplorazioni ignorate sono state introdotte nel EF Core 5,0.

La modifica manuale della tabella di join può essere complessa. A partire da EF Core 5,0, le relazioni molti-a-molti possono essere modificate direttamente usando spostamenti speciali della raccolta che "ignorano" l'entità di join. Ad esempio, è possibile aggiungere due navigazioni Skip al modello precedente. uno da post a tag e l'altro da tag a post:

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

Questa relazione molti-a-molti richiede la seguente configurazione per garantire che le esplorazioni ignorate e le normali esplorazioni siano tutte usate per la stessa relazione molti-a-molti:

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

Per ulteriori informazioni sul mapping di relazioni molti-a-molti, vedere [relazioni](xref:core/modeling/relationships) .

Ignorare le esplorazioni e comportarsi come le normali spostamenti della raccolta. Tuttavia, il modo in cui funzionano con i valori di chiave esterna è diverso. Associare un post a un tag, ma questa volta usando una navigazione Skip:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

Si noti che questo codice non usa l'entità join. Aggiunge invece un'entità a una raccolta di navigazione nello stesso modo in cui verrebbe eseguita se si trattasse di una relazione uno-a-molti. La visualizzazione di debug risultante è essenzialmente identica a quella precedente:

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

Si noti che un'istanza dell' `PostTag` entità join è stata creata automaticamente con i valori FK impostati sui valori PK del tag e post che sono ora associati. Tutte le normali esplorazioni di riferimento e raccolta sono state risolte in base a questi valori FK. Inoltre, poiché questo modello contiene le navigazioni Skip, anche queste sono state risolte. In particolare, anche se è stato aggiunto il tag all' `Post.Tags` esplorazione Skip, la `Tag.Posts` navigazione inversa ignora sull'altro lato di questa relazione è stata anche risolta in modo da contenere il post associato.

Vale la pena notare che le relazioni molti-a-molti sottostanti possono comunque essere modificate direttamente anche quando le esplorazioni Skip sono state sovrapposte al livello superiore. Ad esempio, il tag e il post potrebbero essere associati come prima introduzione a Skip Navigations:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

In alternativa, usare i valori FK:

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

Questa operazione comporterà la corretta correzione degli spostamenti di esplorazione, ottenendo così lo stesso output della visualizzazione di debug dell'esempio precedente.

### <a name="skip-navigations-only"></a>Ignora solo le navigazioni

Nella sezione precedente sono state aggiunte le funzionalità Skip Navigation, _oltre a_ definire completamente le due relazioni uno-a-molti sottostanti. Questa operazione è utile per illustrare cosa accade ai valori FK, ma spesso non è necessario. È invece possibile definire la relazione molti-a-molti utilizzando _solo le navigazioni Skip_. Questo è il modo in cui viene definita la relazione molti-a-molti nel modello nella parte superiore di questo documento. Usando questo modello, è possibile associare di nuovo un post e un tag aggiungendo un post a `Tag.Posts` Skip Navigation (o, in alternativa, aggiungendo un tag a `Post.Tags` Skip Navigation):

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

Esaminando la visualizzazione debug dopo avere apportato questa modifica, si rivela che EF Core ha creato un'istanza di `Dictionary<string, object>` per rappresentare l'entità di join. Questa entità di join contiene `PostsId` entrambe `TagsId` le proprietà di chiave esterna che sono state impostate in modo da corrispondere ai valori PK del post e del tag associati.

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

Per ulteriori informazioni sulle entità di join implicite e sull'utilizzo di tipi di entità, vedere [relazioni](xref:core/modeling/relationships) `Dictionary<string, object>` .

> [!IMPORTANT]
> Il tipo CLR utilizzato per i tipi di entità join per convenzione può cambiare nelle versioni future per migliorare le prestazioni. Non dipendono dal tipo di join `Dictionary<string, object>` , a meno che non sia stato configurato in modo esplicito.

### <a name="join-entities-with-payloads"></a>Unire entità con payload

Fino a questo punto, tutti gli esempi hanno usato un tipo di entità di join (esplicito o implicito) che contiene solo le due proprietà di chiave esterna necessarie per la relazione molti-a-molti. Nessuno di questi valori FK deve essere impostato in modo esplicito dall'applicazione quando si modificano le relazioni perché i relativi valori provengono dalle proprietà di chiave primaria delle entità correlate. Questo consente EF Core di creare istanze dell'entità di join senza dati mancanti.

#### <a name="payloads-with-generated-values"></a>Payload con valori generati

EF Core supporta l'aggiunta di proprietà aggiuntive al tipo di entità join. Questa operazione è nota come assegnazione dell'entità di join a un "payload". Ad esempio, aggiungere `TaggedOn` la proprietà all' `PostTag` entità join:

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

Questa proprietà del payload non verrà impostata quando EF Core crea un'istanza di entità di join. Il modo più comune per gestire questo problema consiste nell'usare le proprietà del payload con i valori generati automaticamente. Ad esempio, la `TaggedOn` proprietà può essere configurata in modo da usare un timestamp generato dall'archivio quando viene inserita ogni nuova entità:

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

È ora possibile contrassegnare un post in modo analogo a quanto prima:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo la chiamata a SaveChanges si indica che la proprietà payload è stata impostata in modo appropriato:

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

#### <a name="explicitly-setting-payload-values"></a>Impostazione esplicita dei valori del payload

Seguendo l'esempio precedente, viene aggiunta una proprietà payload che non usa un valore generato automaticamente:

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

È ora possibile contrassegnare un post in modo analogo a quanto prima e l'entità join verrà comunque creata automaticamente. È quindi possibile accedere a questa entità utilizzando uno dei meccanismi descritti in [accesso alle entità registrate](xref:core/change-tracking/entity-entries). Ad esempio, il codice seguente usa <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> per accedere all'istanza dell'entità di join:

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

Una volta individuata l'entità di join, è possibile modificarla in modo normale, in questo esempio, per impostare la `TaggedBy` Proprietà payload prima di chiamare SaveChanges.

> [!NOTE]
> Si noti che è necessaria una chiamata a per <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> fornire EF core la possibilità di rilevare la modifica della proprietà di navigazione e creare l'istanza dell'entità di join prima di `Find` usare. Per ulteriori informazioni, vedere [rilevamento delle modifiche e notifiche](xref:core/change-tracking/change-detection) .

In alternativa, l'entità di join può essere creata in modo esplicito per associare un post a un tag. Ad esempio:

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

Infine, un altro modo per impostare i dati del payload consiste nell'eseguire <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> l'override o l'utilizzo dell' <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> evento per elaborare le entità prima di aggiornare il database. Ad esempio:

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
