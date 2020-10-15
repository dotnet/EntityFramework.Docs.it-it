---
title: Test con test doppi-EF6
description: Test con i doppi test personalizzati in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 16a237edd4ff8ce72800cfd623de93a08469cfe1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062815"
---
# <a name="testing-with-your-own-test-doubles"></a>Test con i doppi test personalizzati
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Quando si scrivono test per l'applicazione, è spesso consigliabile evitare di raggiungere il database.  Entity Framework consente di ottenere questo risultato creando un contesto, con il comportamento definito dai test, che usa i dati in memoria.  

## <a name="options-for-creating-test-doubles"></a>Opzioni per la creazione di doppi di test  

Esistono due approcci diversi che possono essere usati per creare una versione in memoria del contesto.  

- **Creare doppi test personalizzati** : questo approccio prevede la scrittura di un'implementazione in memoria personalizzata del contesto e di DbSet. In questo modo è possibile controllare in che modo le classi si comportano, ma possono coinvolgere la scrittura e la creazione di una quantità ragionevole di codice.  
- **Usare un Framework fittizio per creare doppi di test** : usando un Framework fittizio, ad esempio MOQ, è possibile disporre delle implementazioni in memoria del contesto e dei set creati dinamicamente in fase di esecuzione.  

In questo articolo viene illustrata la creazione di un doppio test personalizzato. Per informazioni sull'uso di un Framework fittizio, vedere [test con un Framework fittizio](xref:ef6/fundamentals/testing/mocking).  

## <a name="testing-with-pre-ef6-versions"></a>Test con versioni precedenti a EF6  

Il codice illustrato in questo articolo è compatibile con EF6. Per i test con EF5 e versioni precedenti, vedere [test con un contesto fittizio](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitazioni dei duplicati del test EF in memoria  

I doppi test in memoria possono essere un metodo efficace per fornire unit test copertura dei bit dell'applicazione che usano EF. Tuttavia, quando si esegue questa operazione si utilizza LINQ to Objects per eseguire query sui dati in memoria. Questo può comportare un comportamento diverso rispetto all'uso del provider LINQ di EF (LINQ to Entities) per tradurre le query in SQL che viene eseguito sul database.  

Un esempio di tale differenza è il caricamento dei dati correlati. Se si crea una serie di Blog con post correlati, quando si usano i dati in memoria i post correlati verranno sempre caricati per ogni blog. Tuttavia, quando si esegue su un database, i dati verranno caricati solo se si usa il metodo include.  

Per questo motivo, è consigliabile includere sempre un certo livello di test end-to-end (oltre agli unit test) per garantire il corretto funzionamento dell'applicazione in un database.  

## <a name="following-along-with-this-article"></a>Segue con questo articolo  

Questo articolo fornisce elenchi di codici completi che è possibile copiare in Visual Studio per proseguire, se lo si desidera. È più semplice creare un **progetto di unit test** ed è necessario specificare come destinazione **.NET Framework 4,5** per completare le sezioni che usano Async.  

## <a name="creating-a-context-interface"></a>Creazione di un'interfaccia di contesto  

Verranno esaminati i test di un servizio che usa un modello EF. Per poter sostituire il contesto EF con una versione in memoria per il test, si definirà un'interfaccia che verrà implementata dal contesto EF (e il doppio in memoria).

Il servizio che verrà testato eseguirà query e modificherà i dati usando le proprietà DbSet del contesto e chiamerà anche SaveChanges per eseguire il push delle modifiche nel database. Quindi, questi membri verranno inclusi nell'interfaccia.  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a>Modello EF  

Il servizio che verrà testato usa un modello EF costituito da BloggingContext e dalle classi post e Blog. Questo codice potrebbe essere stato generato dalla finestra di progettazione di EF o essere un modello di Code First.  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>Implementazione dell'interfaccia del contesto con la finestra di progettazione EF  

Si noti che il contesto implementa l'interfaccia IBloggingContext.  

Se si usa Code First, è possibile modificare il contesto direttamente per implementare l'interfaccia. Se si usa la finestra di progettazione EF, sarà necessario modificare il modello T4 che genera il contesto. Aprire il \<model_name\> . File Context.tt annidato sotto il file edmx, trovare il frammento di codice seguente e aggiungere l'interfaccia come illustrato.  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
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

## <a name="creating-the-in-memory-test-doubles"></a>Creazione dei duplicati del test in memoria  

Ora che è disponibile il modello EF reale e il servizio che può usarlo, è necessario creare il doppio test in memoria che è possibile usare per il test. È stato creato un doppio test TestContext per il contesto. In test doppi si ottiene la scelta del comportamento desiderato per supportare i test che verrà eseguito. In questo esempio viene semplicemente acquisito il numero di volte in cui viene chiamato SaveChanges, ma è possibile includere qualsiasi logica necessaria per verificare lo scenario che si sta testando.  

È stato creato anche un TestDbSet che fornisce un'implementazione in memoria di DbSet. È stata fornita un'implementazione completa per tutti i metodi in DbSet (ad eccezione di Find), ma è necessario implementare solo i membri che lo scenario di test utilizzerà.  

TestDbSet usa alcune altre classi di infrastruttura incluse per garantire che le query asincrone possano essere elaborate.  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

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

### <a name="implementing-find"></a>Implementazione di Find  

Il metodo Find è difficile da implementare in modo generico. Se è necessario testare il codice che usa il metodo Find, è più facile creare un DbSet di test per ognuno dei tipi di entità che devono supportare Find. È quindi possibile scrivere la logica per trovare quel particolare tipo di entità, come illustrato di seguito.  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a>Scrittura di alcuni test  

Questo è tutto ciò che serve per avviare i test. Il test seguente consente di creare un TestContext e quindi un servizio basato su questo contesto. Il servizio viene quindi usato per creare un nuovo Blog, usando il metodo AddBlog. Infine, il test verifica che il servizio abbia aggiunto un nuovo Blog alla proprietà Blogs del contesto e abbia chiamato SaveChanges nel contesto.  

Questo è solo un esempio dei tipi di elementi che è possibile testare con un doppio di test in memoria ed è possibile modificare la logica dei doppi test e la verifica per soddisfare i requisiti.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

Ecco un altro esempio di test: questa volta che esegue una query. Il test inizia creando un contesto di test con alcuni dati nella relativa proprietà Blog. si noti che i dati non sono in ordine alfabetico. Possiamo quindi creare un BlogService in base al contesto di test e assicurarci che i dati restituiti da GetAllBlogs siano ordinati in base al nome.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

Infine, si scriverà un altro test che usa il metodo asincrono per garantire che l'infrastruttura asincrona inclusa in [TestDbSet](#creating-the-in-memory-test-doubles) funzioni.  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
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
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
