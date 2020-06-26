---
title: Caricamento di dati correlati - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370473"
---
# <a name="loading-related-data"></a>Caricamento di dati correlati

Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate. Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.

* **Caricamento eager** significa che i dati correlati vengono caricati dal database come parte della query iniziale.
* **Caricamento esplicito** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.
* **Caricamento lazy** significa che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.

## <a name="eager-loading"></a>Caricamento eager

È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query. Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto. Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.

È possibile includere dati correlati da più relazioni in una singola query.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>Inclusione di più livelli

È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`. L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

È possibile combinare tutto ciò per includere dati correlati da più livelli e più nodi radice nella stessa query.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

È possibile che si vogliano includere più entità correlate per una delle entità incluse. Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`. A tale scopo, occorre specificare ogni percorso di inclusione iniziando dalla radice. Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`. Questo non significa che si otterranno join ridondanti. Nella maggior parte dei casi EF consoliderà i join durante la generazione di SQL.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> A partire dalla versione 3.0.0, ognuno di essi `Include` provocherà l'aggiunta di un join aggiuntivo alle query SQL prodotte dai provider relazionali, mentre le versioni precedenti generavano query SQL aggiuntive. Questo può modificare in modo significativo le prestazioni delle query, per un miglioramento o peggio. In particolare, potrebbe essere necessario suddividere le query LINQ con un numero di operatori estremamente elevato in `Include` più query LINQ separate per evitare il problema di esplosione cartesiana.

### <a name="filtered-include"></a>Inclusione filtrato

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

Quando si applica l'inclusione per caricare dati correlati, è possibile applicare determinate operazioni enumerabili sulla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.

Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .

Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro. Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi: 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

In alternativa, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](tracking.md). Tutte le entità rilevanti che sono state querried per le versioni precedenti e sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro. Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.

Esempio:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a>Inclusione per i tipi derivati

È possibile includere dati correlati dalle navigazioni definite solo per un tipo derivato mediante `Include` e `ThenInclude`.

Dato il modello seguente:

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:

* Cast

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* Operatore `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* Overload di `Include` che accetta parametri di tipo `string`

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>Caricamento esplicito

È possibile caricare in modo esplicito una proprietà di navigazione tramite l'API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

È anche possibile caricare in modo esplicito una proprietà di navigazione eseguendo una query separata che restituisce le entità correlate. Se è abilitato il rilevamento delle modifiche, durante il caricamento di un'entità EF Core imposterà automaticamente le proprietà di navigazione dell'entità appena caricata in modo da fare riferimento alle entità già caricate e imposterà le proprietà di navigazione delle entità già caricate in modo da fare riferimento all'entità appena caricata.

### <a name="querying-related-entities"></a>Esecuzione di query su entità correlate

È anche possibile ottenere una query LINQ che rappresenta il contenuto di una proprietà di navigazione.

Ciò consente di eseguire operazioni quali l'esecuzione di un operatore di aggregazione sulle entità correlate senza caricarle in memoria.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

È anche possibile filtrare le entità correlate che vengono caricate in memoria.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>Caricamento lazy

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

### <a name="lazy-loading-without-proxies"></a>Caricamento lazy senza proxy

I proxy di caricamento lazy operano inserendo il servizio `ILazyLoader` in un'entità, come descritto in [Costruttori di tipi di entità](../modeling/constructors.md). Ad esempio:

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

In questo caso non è richiesto che i tipi di entità vengano ereditati o che le proprietà di navigazione siano virtuali e le istanze di entità possono essere create con `new` per eseguire il caricamento lazy dopo il collegamento a un contesto. Tuttavia, è necessario un riferimento al servizio `ILazyLoader`, che viene definito nel pacchetto [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/). Questo pacchetto contiene un set minimo di tipi in modo che vi sia un impatto minimo per le dipendenze. Tuttavia, per evitare completamente di dipendere da pacchetti di EF Core nei tipi di entità, è possibile inserire il metodo `ILazyLoader.Load` come delegato. Ad esempio:

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

## <a name="related-data-and-serialization"></a>Dati correlati e serializzazione

Dato che EF Core correggerà automaticamente le proprietà di navigazione, è possibile che il grafo degli oggetti contenga cicli. Ad esempio, il caricamento di un blog e dei post correlati risulterà in un oggetto blog che fa riferimento a una raccolta di post. Ognuno di tali post avrà un riferimento al blog.

Alcuni framework di serializzazione non consentono tali cicli. Ad esempio, Json.NET genererà l'eccezione seguente se viene rilevato un ciclo.

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: ciclo autoreferenziale rilevato per la proprietà 'Blog' con tipo 'MyApplication.Models.Blog')

Se si usa ASP.NET Core, è possibile configurare Json.NET per ignorare i cicli trovati nel grafo degli oggetti. Questa operazione viene eseguita nel metodo `ConfigureServices(...)` in `Startup.cs`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

In alternativa è possibile aggiungere l'attributo `[JsonIgnore]` a una proprietà di navigazione, per indicare a Json.NET di non attraversare la proprietà di navigazione durante la serializzazione.
