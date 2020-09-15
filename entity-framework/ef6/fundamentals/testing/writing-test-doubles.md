---
title: Test con test doppi-EF6
description: Test con i doppi test personalizzati in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 575007c5568049e6508d1bd6435597a1acd843ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070432"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="75262-103">Test con i doppi test personalizzati</span><span class="sxs-lookup"><span data-stu-id="75262-103">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="75262-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="75262-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="75262-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="75262-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="75262-106">Quando si scrivono test per l'applicazione, è spesso consigliabile evitare di raggiungere il database.</span><span class="sxs-lookup"><span data-stu-id="75262-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="75262-107">Entity Framework consente di ottenere questo risultato creando un contesto, con il comportamento definito dai test, che usa i dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="75262-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="75262-108">Opzioni per la creazione di doppi di test</span><span class="sxs-lookup"><span data-stu-id="75262-108">Options for creating test doubles</span></span>  

<span data-ttu-id="75262-109">Esistono due approcci diversi che possono essere usati per creare una versione in memoria del contesto.</span><span class="sxs-lookup"><span data-stu-id="75262-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="75262-110">**Creare doppi test personalizzati** : questo approccio prevede la scrittura di un'implementazione in memoria personalizzata del contesto e di DbSet.</span><span class="sxs-lookup"><span data-stu-id="75262-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="75262-111">In questo modo è possibile controllare in che modo le classi si comportano, ma possono coinvolgere la scrittura e la creazione di una quantità ragionevole di codice.</span><span class="sxs-lookup"><span data-stu-id="75262-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="75262-112">**Usare un Framework fittizio per creare doppi di test** : usando un Framework fittizio, ad esempio MOQ, è possibile disporre delle implementazioni in memoria del contesto e dei set creati dinamicamente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="75262-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="75262-113">In questo articolo viene illustrata la creazione di un doppio test personalizzato.</span><span class="sxs-lookup"><span data-stu-id="75262-113">This article will deal with creating your own test double.</span></span> <span data-ttu-id="75262-114">Per informazioni sull'uso di un Framework fittizio, vedere [test con un Framework fittizio](xref:ef6/fundamentals/testing/mocking).</span><span class="sxs-lookup"><span data-stu-id="75262-114">For information on using a mocking framework see [Testing with a Mocking Framework](xref:ef6/fundamentals/testing/mocking).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="75262-115">Test con versioni precedenti a EF6</span><span class="sxs-lookup"><span data-stu-id="75262-115">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="75262-116">Il codice illustrato in questo articolo è compatibile con EF6.</span><span class="sxs-lookup"><span data-stu-id="75262-116">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="75262-117">Per i test con EF5 e versioni precedenti, vedere [test con un contesto fittizio](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="75262-117">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="75262-118">Limitazioni dei duplicati del test EF in memoria</span><span class="sxs-lookup"><span data-stu-id="75262-118">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="75262-119">I doppi test in memoria possono essere un metodo efficace per fornire unit test copertura dei bit dell'applicazione che usano EF.</span><span class="sxs-lookup"><span data-stu-id="75262-119">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="75262-120">Tuttavia, quando si esegue questa operazione si utilizza LINQ to Objects per eseguire query sui dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="75262-120">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="75262-121">Questo può comportare un comportamento diverso rispetto all'uso del provider LINQ di EF (LINQ to Entities) per tradurre le query in SQL che viene eseguito sul database.</span><span class="sxs-lookup"><span data-stu-id="75262-121">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="75262-122">Un esempio di tale differenza è il caricamento dei dati correlati.</span><span class="sxs-lookup"><span data-stu-id="75262-122">One example of such a difference is loading related data.</span></span> <span data-ttu-id="75262-123">Se si crea una serie di Blog con post correlati, quando si usano i dati in memoria i post correlati verranno sempre caricati per ogni blog.</span><span class="sxs-lookup"><span data-stu-id="75262-123">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="75262-124">Tuttavia, quando si esegue su un database, i dati verranno caricati solo se si usa il metodo include.</span><span class="sxs-lookup"><span data-stu-id="75262-124">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="75262-125">Per questo motivo, è consigliabile includere sempre un certo livello di test end-to-end (oltre agli unit test) per garantire il corretto funzionamento dell'applicazione in un database.</span><span class="sxs-lookup"><span data-stu-id="75262-125">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="75262-126">Segue con questo articolo</span><span class="sxs-lookup"><span data-stu-id="75262-126">Following along with this article</span></span>  

<span data-ttu-id="75262-127">Questo articolo fornisce elenchi di codici completi che è possibile copiare in Visual Studio per proseguire, se lo si desidera.</span><span class="sxs-lookup"><span data-stu-id="75262-127">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="75262-128">È più semplice creare un **progetto di unit test** ed è necessario specificare come destinazione **.NET Framework 4,5** per completare le sezioni che usano Async.</span><span class="sxs-lookup"><span data-stu-id="75262-128">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="75262-129">Creazione di un'interfaccia di contesto</span><span class="sxs-lookup"><span data-stu-id="75262-129">Creating a context interface</span></span>  

<span data-ttu-id="75262-130">Verranno esaminati i test di un servizio che usa un modello EF.</span><span class="sxs-lookup"><span data-stu-id="75262-130">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="75262-131">Per poter sostituire il contesto EF con una versione in memoria per il test, si definirà un'interfaccia che verrà implementata dal contesto EF (e il doppio in memoria).</span><span class="sxs-lookup"><span data-stu-id="75262-131">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="75262-132">Il servizio che verrà testato eseguirà query e modificherà i dati usando le proprietà DbSet del contesto e chiamerà anche SaveChanges per eseguire il push delle modifiche nel database.</span><span class="sxs-lookup"><span data-stu-id="75262-132">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="75262-133">Quindi, questi membri verranno inclusi nell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="75262-133">So we're including these members on the interface.</span></span>  

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

## <a name="the-ef-model"></a><span data-ttu-id="75262-134">Modello EF</span><span class="sxs-lookup"><span data-stu-id="75262-134">The EF model</span></span>  

<span data-ttu-id="75262-135">Il servizio che verrà testato usa un modello EF costituito da BloggingContext e dalle classi post e Blog.</span><span class="sxs-lookup"><span data-stu-id="75262-135">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="75262-136">Questo codice potrebbe essere stato generato dalla finestra di progettazione di EF o essere un modello di Code First.</span><span class="sxs-lookup"><span data-stu-id="75262-136">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="75262-137">Implementazione dell'interfaccia del contesto con la finestra di progettazione EF</span><span class="sxs-lookup"><span data-stu-id="75262-137">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="75262-138">Si noti che il contesto implementa l'interfaccia IBloggingContext.</span><span class="sxs-lookup"><span data-stu-id="75262-138">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="75262-139">Se si usa Code First, è possibile modificare il contesto direttamente per implementare l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="75262-139">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="75262-140">Se si usa la finestra di progettazione EF, sarà necessario modificare il modello T4 che genera il contesto.</span><span class="sxs-lookup"><span data-stu-id="75262-140">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="75262-141">Aprire il \<model_name\> . File Context.tt annidato sotto il file edmx, trovare il frammento di codice seguente e aggiungere l'interfaccia come illustrato.</span><span class="sxs-lookup"><span data-stu-id="75262-141">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="75262-142">Servizio da testare</span><span class="sxs-lookup"><span data-stu-id="75262-142">Service to be tested</span></span>  

<span data-ttu-id="75262-143">Per illustrare i test con i doppi test in memoria, verranno scritti due test per un BlogService.</span><span class="sxs-lookup"><span data-stu-id="75262-143">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="75262-144">Il servizio è in grado di creare nuovi Blog (AddBlog) e di restituire tutti i Blog ordinati in base al nome (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="75262-144">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="75262-145">Oltre a GetAllBlogs, è stato inoltre fornito un metodo che consente di ottenere in modo asincrono tutti i Blog ordinati in base al nome (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="75262-145">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="75262-146">Creazione dei duplicati del test in memoria</span><span class="sxs-lookup"><span data-stu-id="75262-146">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="75262-147">Ora che è disponibile il modello EF reale e il servizio che può usarlo, è necessario creare il doppio test in memoria che è possibile usare per il test.</span><span class="sxs-lookup"><span data-stu-id="75262-147">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="75262-148">È stato creato un doppio test TestContext per il contesto.</span><span class="sxs-lookup"><span data-stu-id="75262-148">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="75262-149">In test doppi si ottiene la scelta del comportamento desiderato per supportare i test che verrà eseguito.</span><span class="sxs-lookup"><span data-stu-id="75262-149">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="75262-150">In questo esempio viene semplicemente acquisito il numero di volte in cui viene chiamato SaveChanges, ma è possibile includere qualsiasi logica necessaria per verificare lo scenario che si sta testando.</span><span class="sxs-lookup"><span data-stu-id="75262-150">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="75262-151">È stato creato anche un TestDbSet che fornisce un'implementazione in memoria di DbSet.</span><span class="sxs-lookup"><span data-stu-id="75262-151">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="75262-152">È stata fornita un'implementazione completa per tutti i metodi in DbSet (ad eccezione di Find), ma è necessario implementare solo i membri che lo scenario di test utilizzerà.</span><span class="sxs-lookup"><span data-stu-id="75262-152">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="75262-153">TestDbSet usa alcune altre classi di infrastruttura incluse per garantire che le query asincrone possano essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="75262-153">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

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

### <a name="implementing-find"></a><span data-ttu-id="75262-154">Implementazione di Find</span><span class="sxs-lookup"><span data-stu-id="75262-154">Implementing Find</span></span>  

<span data-ttu-id="75262-155">Il metodo Find è difficile da implementare in modo generico.</span><span class="sxs-lookup"><span data-stu-id="75262-155">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="75262-156">Se è necessario testare il codice che usa il metodo Find, è più facile creare un DbSet di test per ognuno dei tipi di entità che devono supportare Find.</span><span class="sxs-lookup"><span data-stu-id="75262-156">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="75262-157">È quindi possibile scrivere la logica per trovare quel particolare tipo di entità, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="75262-157">You can then write logic to find that particular type of entity, as shown below.</span></span>  

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

## <a name="writing-some-tests"></a><span data-ttu-id="75262-158">Scrittura di alcuni test</span><span class="sxs-lookup"><span data-stu-id="75262-158">Writing some tests</span></span>  

<span data-ttu-id="75262-159">Questo è tutto ciò che serve per avviare i test.</span><span class="sxs-lookup"><span data-stu-id="75262-159">That’s all we need to do to start testing.</span></span> <span data-ttu-id="75262-160">Il test seguente consente di creare un TestContext e quindi un servizio basato su questo contesto.</span><span class="sxs-lookup"><span data-stu-id="75262-160">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="75262-161">Il servizio viene quindi usato per creare un nuovo Blog, usando il metodo AddBlog.</span><span class="sxs-lookup"><span data-stu-id="75262-161">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="75262-162">Infine, il test verifica che il servizio abbia aggiunto un nuovo Blog alla proprietà Blogs del contesto e abbia chiamato SaveChanges nel contesto.</span><span class="sxs-lookup"><span data-stu-id="75262-162">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="75262-163">Questo è solo un esempio dei tipi di elementi che è possibile testare con un doppio di test in memoria ed è possibile modificare la logica dei doppi test e la verifica per soddisfare i requisiti.</span><span class="sxs-lookup"><span data-stu-id="75262-163">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

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

<span data-ttu-id="75262-164">Ecco un altro esempio di test: questa volta che esegue una query.</span><span class="sxs-lookup"><span data-stu-id="75262-164">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="75262-165">Il test inizia creando un contesto di test con alcuni dati nella relativa proprietà Blog. si noti che i dati non sono in ordine alfabetico.</span><span class="sxs-lookup"><span data-stu-id="75262-165">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="75262-166">Possiamo quindi creare un BlogService in base al contesto di test e assicurarci che i dati restituiti da GetAllBlogs siano ordinati in base al nome.</span><span class="sxs-lookup"><span data-stu-id="75262-166">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

<span data-ttu-id="75262-167">Infine, si scriverà un altro test che usa il metodo asincrono per garantire che l'infrastruttura asincrona inclusa in [TestDbSet](#creating-the-in-memory-test-doubles) funzioni.</span><span class="sxs-lookup"><span data-stu-id="75262-167">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

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
