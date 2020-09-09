---
title: Tipi di entità con costruttori-EF Core
description: Utilizzo di costruttori per associare dati a Entity Framework Core modello
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 58529a3a68e69a31249460d402027274404dce45
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617540"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="a7ed6-103">Tipi di entità con costruttori</span><span class="sxs-lookup"><span data-stu-id="a7ed6-103">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="a7ed6-104">Questa funzionalità è stata introdotta in EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="a7ed6-105">A partire da EF Core 2,1, è ora possibile definire un costruttore con parametri e EF Core chiamare questo costruttore quando si crea un'istanza dell'entità.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-105">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="a7ed6-106">I parametri del costruttore possono essere associati a proprietà mappate o a vari tipi di servizi per semplificare i comportamenti come il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-106">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="a7ed6-107">A partire da EF Core 2,1, l'associazione di tutti i costruttori è per convenzione.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-107">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="a7ed6-108">La configurazione di costruttori specifici da usare è prevista per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-108">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="a7ed6-109">Associazione alle proprietà mappate</span><span class="sxs-lookup"><span data-stu-id="a7ed6-109">Binding to mapped properties</span></span>

<span data-ttu-id="a7ed6-110">Si consideri un modello di Blog/post tipico:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-110">Consider a typical Blog/Post model:</span></span>

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

<span data-ttu-id="a7ed6-111">Quando EF Core crea istanze di questi tipi, ad esempio per i risultati di una query, chiamerà prima di tutto il costruttore senza parametri predefinito e quindi imposterà ogni proprietà sul valore del database.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-111">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="a7ed6-112">Tuttavia, se EF Core trova un costruttore con parametri con i nomi di parametro e i tipi che corrispondono a quelli delle proprietà mappate, chiamerà invece il costruttore con parametri con i valori per tali proprietà e non imposterà in modo esplicito ogni proprietà.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-112">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="a7ed6-113">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-113">For example:</span></span>

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

<span data-ttu-id="a7ed6-114">Note importanti:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-114">Some things to note:</span></span>

* <span data-ttu-id="a7ed6-115">Non è necessario che tutte le proprietà dispongano di parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-115">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="a7ed6-116">La proprietà post. Content, ad esempio, non è impostata da alcun parametro del costruttore, pertanto EF Core lo imposterà in modo normale dopo la chiamata al costruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-116">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="a7ed6-117">I tipi e i nomi dei parametri devono corrispondere ai nomi e ai tipi di proprietà, ad eccezione del fatto che le proprietà possono essere configurate in Pascal mentre i parametri sono con maiuscole/minuscole.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-117">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="a7ed6-118">EF Core non è possibile impostare le proprietà di navigazione, ad esempio Blog o post precedenti, usando un costruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-118">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="a7ed6-119">Il costruttore può essere pubblico, privato o avere qualsiasi altra accessibilità.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-119">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="a7ed6-120">Tuttavia, per i proxy di caricamento lazy è necessario che il costruttore sia accessibile dalla classe proxy di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-120">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="a7ed6-121">In genere ciò significa renderlo pubblico o protetto.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-121">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="a7ed6-122">Proprietà di sola lettura</span><span class="sxs-lookup"><span data-stu-id="a7ed6-122">Read-only properties</span></span>

<span data-ttu-id="a7ed6-123">Quando le proprietà vengono impostate tramite il costruttore, può essere utile renderle di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-123">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="a7ed6-124">EF Core supporta questa operazione, ma è necessario esaminare alcuni aspetti:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-124">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="a7ed6-125">Non è stato eseguito il mapping delle proprietà senza Setter per convenzione.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-125">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="a7ed6-126">Questa operazione tende a eseguire il mapping delle proprietà che non devono essere mappate, ad esempio le proprietà calcolate.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-126">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="a7ed6-127">L'uso di valori di chiave generati automaticamente richiede una proprietà chiave che sia di lettura/scrittura, perché il valore della chiave deve essere impostato dal generatore di chiavi quando si inseriscono nuove entità.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-127">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="a7ed6-128">Un modo semplice per evitare questi problemi consiste nell'usare Setter privati.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-128">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="a7ed6-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-129">For example:</span></span>

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

<span data-ttu-id="a7ed6-130">EF Core Visualizza una proprietà con un setter privato come lettura/scrittura, il che significa che tutte le proprietà sono mappate come prima e la chiave può essere comunque generata dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-130">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="a7ed6-131">Un'alternativa all'uso di Setter privati consiste nel rendere le proprietà di sola lettura e aggiungere più mapping espliciti in OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-131">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="a7ed6-132">Analogamente, alcune proprietà possono essere rimosse completamente e sostituite solo con i campi.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-132">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="a7ed6-133">Si considerino, ad esempio, i tipi di entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-133">For example, consider these entity types:</span></span>

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

<span data-ttu-id="a7ed6-134">E questa configurazione in OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-134">And this configuration in OnModelCreating:</span></span>

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

<span data-ttu-id="a7ed6-135">Aspetti da considerare:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-135">Things to note:</span></span>

* <span data-ttu-id="a7ed6-136">La chiave "Property" è ora un campo.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-136">The key "property" is now a field.</span></span> <span data-ttu-id="a7ed6-137">Non è un `readonly` campo in modo che sia possibile usare le chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-137">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="a7ed6-138">Le altre proprietà sono proprietà di sola lettura impostate solo nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-138">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="a7ed6-139">Se il valore della chiave primaria viene impostato solo da EF o letto dal database, non è necessario includerlo nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-139">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="a7ed6-140">In questo modo, la chiave "Property" viene lasciata come un campo semplice e si rende chiaro che non deve essere impostata in modo esplicito durante la creazione di nuovi blog o post.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-140">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="a7ed6-141">Questo codice genererà l'avviso del compilatore ' 169' indicante che il campo non viene mai usato.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-141">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="a7ed6-142">Questo può essere ignorato perché in realtà EF Core usa il campo in modo linguistico.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-142">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="a7ed6-143">Inserimento di servizi</span><span class="sxs-lookup"><span data-stu-id="a7ed6-143">Injecting services</span></span>

<span data-ttu-id="a7ed6-144">EF Core inoltre possibile inserire i "servizi" nel costruttore di un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-144">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="a7ed6-145">Ad esempio, è possibile inserire quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-145">For example, the following can be injected:</span></span>

* <span data-ttu-id="a7ed6-146">`DbContext` : istanza del contesto corrente, che può essere tipizzata anche come tipo DbContext derivato</span><span class="sxs-lookup"><span data-stu-id="a7ed6-146">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="a7ed6-147">`ILazyLoader` -servizio di caricamento lazy. per ulteriori informazioni, vedere la [documentazione relativa al caricamento lazy](xref:core/querying/related-data) .</span><span class="sxs-lookup"><span data-stu-id="a7ed6-147">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="a7ed6-148">`Action<object, string>` -un delegato di caricamento lazy. per ulteriori informazioni, vedere la [documentazione relativa al caricamento lazy](xref:core/querying/related-data) .</span><span class="sxs-lookup"><span data-stu-id="a7ed6-148">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="a7ed6-149">`IEntityType` -i metadati di EF Core associati a questo tipo di entità</span><span class="sxs-lookup"><span data-stu-id="a7ed6-149">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="a7ed6-150">A partire da EF Core 2,1, è possibile inserire solo i servizi noti da EF Core.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-150">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="a7ed6-151">Il supporto per l'inserimento di servizi applicativi viene preso in considerazione per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-151">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="a7ed6-152">Ad esempio, è possibile usare un DbContext inserito per accedere in modo selettivo al database per ottenere informazioni sulle entità correlate senza caricarle tutte.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-152">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="a7ed6-153">Nell'esempio seguente viene usato per ottenere il numero di post in un blog senza caricare i post:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-153">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

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

<span data-ttu-id="a7ed6-154">Ecco alcuni aspetti da tenere presente:</span><span class="sxs-lookup"><span data-stu-id="a7ed6-154">A few things to notice about this:</span></span>

* <span data-ttu-id="a7ed6-155">Il costruttore è privato, perché viene chiamato solo da EF Core ed è disponibile un altro costruttore pubblico per uso generale.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-155">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="a7ed6-156">Il codice che usa il servizio inserito (ovvero il contesto) è difensivo rispetto `null` alla gestione dei casi in cui EF Core non crea l'istanza.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-156">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="a7ed6-157">Poiché il servizio viene archiviato in una proprietà di lettura/scrittura, verrà reimpostato quando l'entità è associata a una nuova istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-157">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="a7ed6-158">L'inserimento di DbContext come questo è spesso considerato un anti-pattern, perché abbina i tipi di entità direttamente a EF Core.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-158">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="a7ed6-159">Valutare attentamente tutte le opzioni prima di usare l'inserimento di un servizio come questo.</span><span class="sxs-lookup"><span data-stu-id="a7ed6-159">Carefully consider all options before using service injection like this.</span></span>
