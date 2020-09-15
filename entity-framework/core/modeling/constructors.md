---
title: Tipi di entità con costruttori-EF Core
description: Utilizzo di costruttori per associare dati a Entity Framework Core modello
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 06d18f173275599ad1e547193363e13c48fc8dcf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071589"
---
# <a name="entity-types-with-constructors"></a>Tipi di entità con costruttori

> [!NOTE]  
> Questa funzionalità è stata introdotta in EF Core 2.1.

A partire da EF Core 2,1, è ora possibile definire un costruttore con parametri e EF Core chiamare questo costruttore quando si crea un'istanza dell'entità. I parametri del costruttore possono essere associati a proprietà mappate o a vari tipi di servizi per semplificare i comportamenti come il caricamento lazy.

> [!NOTE]  
> A partire da EF Core 2,1, l'associazione di tutti i costruttori è per convenzione. La configurazione di costruttori specifici da usare è prevista per una versione futura.

## <a name="binding-to-mapped-properties"></a>Associazione alle proprietà mappate

Si consideri un modello di Blog/post tipico:

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Quando EF Core crea istanze di questi tipi, ad esempio per i risultati di una query, chiamerà prima di tutto il costruttore senza parametri predefinito e quindi imposterà ogni proprietà sul valore del database. Tuttavia, se EF Core trova un costruttore con parametri con i nomi di parametro e i tipi che corrispondono a quelli delle proprietà mappate, chiamerà invece il costruttore con parametri con i valori per tali proprietà e non imposterà in modo esplicito ogni proprietà. Esempio:

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Note importanti:

* Non è necessario che tutte le proprietà dispongano di parametri del costruttore. La proprietà post. Content, ad esempio, non è impostata da alcun parametro del costruttore, pertanto EF Core lo imposterà in modo normale dopo la chiamata al costruttore.
* I tipi e i nomi dei parametri devono corrispondere ai nomi e ai tipi di proprietà, ad eccezione del fatto che le proprietà possono essere configurate in Pascal mentre i parametri sono con maiuscole/minuscole.
* EF Core non è possibile impostare le proprietà di navigazione, ad esempio Blog o post precedenti, usando un costruttore.
* Il costruttore può essere pubblico, privato o avere qualsiasi altra accessibilità. Tuttavia, per i proxy di caricamento lazy è necessario che il costruttore sia accessibile dalla classe proxy di ereditarietà. In genere ciò significa renderlo pubblico o protetto.

### <a name="read-only-properties"></a>Proprietà di sola lettura

Quando le proprietà vengono impostate tramite il costruttore, può essere utile renderle di sola lettura. EF Core supporta questa operazione, ma è necessario esaminare alcuni aspetti:

* Non è stato eseguito il mapping delle proprietà senza Setter per convenzione. Questa operazione tende a eseguire il mapping delle proprietà che non devono essere mappate, ad esempio le proprietà calcolate.
* L'uso di valori di chiave generati automaticamente richiede una proprietà chiave che sia di lettura/scrittura, perché il valore della chiave deve essere impostato dal generatore di chiavi quando si inseriscono nuove entità.

Un modo semplice per evitare questi problemi consiste nell'usare Setter privati. Esempio:

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

EF Core Visualizza una proprietà con un setter privato come lettura/scrittura, il che significa che tutte le proprietà sono mappate come prima e la chiave può essere comunque generata dall'archivio.

Un'alternativa all'uso di Setter privati consiste nel rendere le proprietà di sola lettura e aggiungere più mapping espliciti in OnModelCreating. Analogamente, alcune proprietà possono essere rimosse completamente e sostituite solo con i campi. Si considerino, ad esempio, i tipi di entità seguenti:

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

E questa configurazione in OnModelCreating:

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

Aspetti da considerare:

* La chiave "Property" è ora un campo. Non è un `readonly` campo in modo che sia possibile usare le chiavi generate dall'archivio.
* Le altre proprietà sono proprietà di sola lettura impostate solo nel costruttore.
* Se il valore della chiave primaria viene impostato solo da EF o letto dal database, non è necessario includerlo nel costruttore. In questo modo, la chiave "Property" viene lasciata come un campo semplice e si rende chiaro che non deve essere impostata in modo esplicito durante la creazione di nuovi blog o post.

> [!NOTE]  
> Questo codice genererà l'avviso del compilatore ' 169' indicante che il campo non viene mai usato. Questo può essere ignorato perché in realtà EF Core usa il campo in modo linguistico.

## <a name="injecting-services"></a>Inserimento di servizi

EF Core inoltre possibile inserire i "servizi" nel costruttore di un tipo di entità. Ad esempio, è possibile inserire quanto segue:

* `DbContext` : istanza del contesto corrente, che può essere tipizzata anche come tipo DbContext derivato
* `ILazyLoader` -servizio di caricamento lazy. per ulteriori informazioni, vedere la [documentazione relativa al caricamento lazy](xref:core/querying/related-data) .
* `Action<object, string>` -un delegato di caricamento lazy. per ulteriori informazioni, vedere la [documentazione relativa al caricamento lazy](xref:core/querying/related-data) .
* `IEntityType` -i metadati di EF Core associati a questo tipo di entità

> [!NOTE]  
> A partire da EF Core 2,1, è possibile inserire solo i servizi noti da EF Core. Il supporto per l'inserimento di servizi applicativi viene preso in considerazione per una versione futura.

Ad esempio, è possibile usare un DbContext inserito per accedere in modo selettivo al database per ottenere informazioni sulle entità correlate senza caricarle tutte. Nell'esempio seguente viene usato per ottenere il numero di post in un blog senza caricare i post:

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

Ecco alcuni aspetti da tenere presente:

* Il costruttore è privato, perché viene chiamato solo da EF Core ed è disponibile un altro costruttore pubblico per uso generale.
* Il codice che usa il servizio inserito (ovvero il contesto) è difensivo rispetto `null` alla gestione dei casi in cui EF Core non crea l'istanza.
* Poiché il servizio viene archiviato in una proprietà di lettura/scrittura, verrà reimpostato quando l'entità è associata a una nuova istanza del contesto.

> [!WARNING]  
> L'inserimento di DbContext come questo è spesso considerato un anti-pattern, perché abbina i tipi di entità direttamente a EF Core. Valutare attentamente tutte le opzioni prima di usare l'inserimento di un servizio come questo.
