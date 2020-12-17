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
# <a name="lazy-loading-of-related-data"></a>Caricamento lazy dei dati correlati

## <a name="lazy-loading-with-proxies"></a>Caricamento lazy con proxy

Il modo più semplice per usare il caricamento lazy consiste nell'installare il pacchetto [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitarlo con una chiamata a `UseLazyLoadingProxies`. Ad esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

O quando si usa AddDbContext:

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core abiliterà quindi il caricamento lazy per qualsiasi proprietà di navigazione che può essere sottoposta a override, ovvero deve essere `virtual` e in una classe ereditabile. Ad esempio, nelle entità seguenti, le proprietà di navigazione `Post.Blog` e `Blog.Posts` vengono caricate in modalità lazy.

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
> Il caricamento lazy può causare l'esecuzione di round trip di database aggiuntivi non necessari (il cosiddetto N + 1 problema) e prestare attenzione per evitare questa situazione. Per ulteriori informazioni, vedere la sezione relativa alle [prestazioni](xref:core/performance/efficient-querying#beware-of-lazy-loading) .

## <a name="lazy-loading-without-proxies"></a>Caricamento lazy senza proxy

I proxy di caricamento lazy operano inserendo il servizio `ILazyLoader` in un'entità, come descritto in [Costruttori di tipi di entità](xref:core/modeling/constructors). Ad esempio:

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

Questo metodo non richiede che i tipi di entità vengano ereditati dalle proprietà di navigazione o che siano virtuali e consente alle istanze di entità create con `new` il caricamento lazy una volta associate a un contesto. Tuttavia, è necessario un riferimento al servizio `ILazyLoader`, che viene definito nel pacchetto [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/). Questo pacchetto contiene un set minimo di tipi in modo che non vi sia un piccolo effetto in a seconda di esso. Tuttavia, per evitare completamente a seconda dei pacchetti EF Core nei tipi di entità, è possibile inserire il `ILazyLoader.Load` metodo come delegato. Ad esempio:

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

Il codice precedente usa un metodo di estensione `Load` per chiarire l'uso del delegato:

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
> Il parametro del costruttore per il delegato di caricamento lazy deve essere chiamato "lazyLoader". La possibilità di configurare l'uso di un nome diverso è pianificata per una versione futura.
