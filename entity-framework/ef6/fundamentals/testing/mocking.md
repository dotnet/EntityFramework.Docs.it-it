---
title: Test con un Framework fittizio-EF6
description: Test con un Framework fittizio in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 01890ab3bb8dbf0caa7b3eff797e53b06bc8ec9b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618308"
---
# <a name="testing-with-a-mocking-framework"></a>Test con un Framework fittizio
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Quando si scrivono test per l'applicazione, è spesso consigliabile evitare di raggiungere il database.  Entity Framework consente di ottenere questo risultato creando un contesto, con il comportamento definito dai test, che usa i dati in memoria.  

## <a name="options-for-creating-test-doubles"></a>Opzioni per la creazione di doppi di test  

Esistono due approcci diversi che possono essere usati per creare una versione in memoria del contesto.  

- **Creare doppi test personalizzati** : questo approccio prevede la scrittura di un'implementazione in memoria personalizzata del contesto e di DbSet. In questo modo è possibile controllare in che modo le classi si comportano, ma possono coinvolgere la scrittura e la creazione di una quantità ragionevole di codice.  
- **Usare un Framework fittizio per creare doppi di test** : usando un Framework fittizio, ad esempio MOQ, è possibile avere le implementazioni in memoria del contesto e i set creati dinamicamente in fase di esecuzione.  

In questo articolo viene affrontato l'uso di un Framework fittizio. Per creare doppi test personalizzati, vedere [test con i doppi test personalizzati](xref:ef6/fundamentals/testing/writing-test-doubles).  

Per illustrare l'uso di EF con un Framework fittizio, verrà usato MOQ. Il modo più semplice per ottenere MOQ consiste nell'installare il [pacchetto MOQ da NuGet](https://nuget.org/packages/Moq/).  

## <a name="testing-with-pre-ef6-versions"></a>Test con versioni precedenti a EF6  

Lo scenario illustrato in questo articolo dipende da alcune modifiche apportate a DbSet in EF6. Per i test con EF5 e versioni precedenti, vedere [test con un contesto fittizio](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitazioni dei duplicati del test EF in memoria  

I doppi test in memoria possono essere un metodo efficace per fornire unit test copertura dei bit dell'applicazione che usano EF. Tuttavia, quando si esegue questa operazione si utilizza LINQ to Objects per eseguire query sui dati in memoria. Questo può comportare un comportamento diverso rispetto all'uso del provider LINQ di EF (LINQ to Entities) per tradurre le query in SQL che viene eseguito sul database.  

Un esempio di tale differenza è il caricamento dei dati correlati. Se si crea una serie di Blog con post correlati, quando si usano i dati in memoria i post correlati verranno sempre caricati per ogni blog. Tuttavia, quando si esegue su un database, i dati verranno caricati solo se si usa il metodo include.  

Per questo motivo, è consigliabile includere sempre un certo livello di test end-to-end (oltre agli unit test) per garantire il corretto funzionamento dell'applicazione in un database.  

## <a name="following-along-with-this-article"></a>Segue con questo articolo  

Questo articolo fornisce elenchi di codici completi che è possibile copiare in Visual Studio per proseguire, se lo si desidera. È più semplice creare un **progetto di unit test** ed è necessario specificare come destinazione **.NET Framework 4,5** per completare le sezioni che usano Async.  

## <a name="the-ef-model"></a>Modello EF  

Il servizio che verrà testato usa un modello EF costituito da BloggingContext e dalle classi post e Blog. Questo codice potrebbe essere stato generato dalla finestra di progettazione di EF o essere un modello di Code First.  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="virtual-dbset-properties-with-ef-designer"></a>Proprietà di DbSet virtuali con la finestra di progettazione EF  

Si noti che le proprietà DbSet nel contesto sono contrassegnate come virtuali. Questo consentirà al Framework fittizio di derivare dal contesto ed eseguire l'override di queste proprietà con un'implementazione fittizia.  

Se si usa Code First, è possibile modificare direttamente le classi. Se si usa la finestra di progettazione EF, sarà necessario modificare il modello T4 che genera il contesto. Aprire il \<model_name\> . File Context.tt annidato sotto il file edmx, trovare il frammento di codice seguente e aggiungere la parola chiave virtual come illustrato.  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a>Servizio da testare  

Per illustrare i test con i doppi test in memoria, verranno scritti due test per un BlogService. Il servizio è in grado di creare nuovi Blog (AddBlog) e di restituire tutti i Blog ordinati in base al nome (GetAllBlogs). Oltre a GetAllBlogs, è stato inoltre fornito un metodo che consente di ottenere in modo asincrono tutti i Blog ordinati in base al nome (GetAllBlogsAsync).  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

## <a name="testing-non-query-scenarios"></a>Test di scenari non di query  

Questo è tutto ciò che è necessario fare per iniziare a testare i metodi non query. Il test seguente usa MOQ per creare un contesto. Crea quindi un DbSet \<Blog\> e lo collega per essere restituito dalla proprietà Blogs del contesto. Successivamente, il contesto viene usato per creare un nuovo BlogService che viene quindi usato per creare un nuovo Blog, usando il metodo AddBlog. Infine, il test verifica che il servizio abbia aggiunto un nuovo blog e abbia chiamato SaveChanges nel contesto.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a>Test di scenari di query  

Per poter eseguire query sul doppio del test di DbSet, è necessario configurare un'implementazione di IQueryable. Il primo passaggio consiste nel creare alcuni dati in memoria, ovvero l'uso di un elenco \<Blog\> . Successivamente, creiamo un contesto e DBSet \<Blog\> quindi l'implementazione di IQueryable per DBSet, che delegano solo al provider LINQ to Objects che funziona con List \<T\> .  

È quindi possibile creare un BlogService in base ai doppi test e verificare che i dati restituiti da GetAllBlogs siano ordinati in base al nome.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a>Test con query asincrone

In Entity Framework 6 è stato introdotto un set di metodi di estensione che è possibile utilizzare per eseguire una query in modo asincrono. Esempi di questi metodi includono ToListAsync, FirstAsync, ForEachAsync e così via.  

Poiché Entity Framework query utilizzano LINQ, i metodi di estensione sono definiti in IQueryable e IEnumerable. Tuttavia, poiché sono progettati solo per essere utilizzati con Entity Framework è possibile che venga visualizzato l'errore seguente se si tenta di utilizzarli in una query LINQ che non è una query Entity Framework:

> L'oggetto IQueryable di origine non implementa IDbAsyncEnumerable {0} . Solo le origini che implementano IDbAsyncEnumerable possono essere usate per Entity Framework operazioni asincrone. Per ulteriori informazioni [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) , vedere.  

Mentre i metodi asincroni sono supportati solo quando si esegue su una query EF, è consigliabile usarli nel unit test quando vengono eseguiti su un test in memoria Double di un DbSet.  

Per usare i metodi asincroni è necessario creare un DbAsyncQueryProvider in memoria per elaborare la query asincrona. Sebbene sia possibile configurare un provider di query usando MOQ, è molto più semplice creare un'implementazione di test doppia nel codice. Il codice per questa implementazione è il seguente:  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

Ora che è disponibile un provider di query asincrono, è possibile scrivere un unit test per il nuovo metodo GetAllBlogsAsync.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
