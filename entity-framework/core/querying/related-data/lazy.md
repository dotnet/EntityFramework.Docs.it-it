---
title: Caricamento lazy dei dati correlati-EF Core
description: Caricamento lazy dei dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: 55622b9c5a8f70ef4e7246d6eb14678036948f18
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635458"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="6704f-103">Caricamento lazy dei dati correlati</span><span class="sxs-lookup"><span data-stu-id="6704f-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="6704f-104">Caricamento lazy con proxy</span><span class="sxs-lookup"><span data-stu-id="6704f-104">Lazy loading with proxies</span></span>

<span data-ttu-id="6704f-105">Il modo più semplice per usare il caricamento lazy consiste nell'installare il pacchetto [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitarlo con una chiamata a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="6704f-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="6704f-106">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6704f-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="6704f-107">O quando si usa AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="6704f-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="6704f-108">EF Core abiliterà quindi il caricamento lazy per qualsiasi proprietà di navigazione che può essere sottoposta a override, ovvero deve essere `virtual` e in una classe ereditabile.</span><span class="sxs-lookup"><span data-stu-id="6704f-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="6704f-109">Ad esempio, nelle entità seguenti, le proprietà di navigazione `Post.Blog` e `Blog.Posts` vengono caricate in modalità lazy.</span><span class="sxs-lookup"><span data-stu-id="6704f-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

> [!WARNING]
> <span data-ttu-id="6704f-110">Il caricamento lazy può causare l'esecuzione di round trip di database aggiuntivi non necessari (il cosiddetto N + 1 problema) e prestare attenzione per evitare questa situazione.</span><span class="sxs-lookup"><span data-stu-id="6704f-110">Lazy loading can cause unneeded extra database roundtrips to occur (the so-called N+1 problem), and care should be taken to avoid this.</span></span> <span data-ttu-id="6704f-111">Per ulteriori informazioni, vedere la sezione relativa alle [prestazioni](xref:core/performance/efficient-querying#beware-of-lazy-loading) .</span><span class="sxs-lookup"><span data-stu-id="6704f-111">See the [performance section](xref:core/performance/efficient-querying#beware-of-lazy-loading) for more details.</span></span>

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="6704f-112">Caricamento lazy senza proxy</span><span class="sxs-lookup"><span data-stu-id="6704f-112">Lazy loading without proxies</span></span>

<span data-ttu-id="6704f-113">I proxy di caricamento lazy operano inserendo il servizio `ILazyLoader` in un'entità, come descritto in [Costruttori di tipi di entità](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="6704f-113">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="6704f-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6704f-114">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="6704f-115">Questo metodo non richiede che i tipi di entità vengano ereditati dalle proprietà di navigazione o che siano virtuali e consente alle istanze di entità create con `new` il caricamento lazy una volta associate a un contesto.</span><span class="sxs-lookup"><span data-stu-id="6704f-115">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="6704f-116">Tuttavia, è necessario un riferimento al servizio `ILazyLoader`, che viene definito nel pacchetto [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="6704f-116">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="6704f-117">Questo pacchetto contiene un set minimo di tipi in modo che non vi sia un piccolo effetto in a seconda di esso.</span><span class="sxs-lookup"><span data-stu-id="6704f-117">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="6704f-118">Tuttavia, per evitare completamente a seconda dei pacchetti EF Core nei tipi di entità, è possibile inserire il `ILazyLoader.Load` metodo come delegato.</span><span class="sxs-lookup"><span data-stu-id="6704f-118">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="6704f-119">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6704f-119">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="6704f-120">Il codice precedente usa un metodo di estensione `Load` per chiarire l'uso del delegato:</span><span class="sxs-lookup"><span data-stu-id="6704f-120">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="6704f-121">Il parametro del costruttore per il delegato di caricamento lazy deve essere chiamato "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="6704f-121">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="6704f-122">La possibilità di configurare l'uso di un nome diverso è pianificata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="6704f-122">Configuration to use a different name than this is planned for a future release.</span></span>
