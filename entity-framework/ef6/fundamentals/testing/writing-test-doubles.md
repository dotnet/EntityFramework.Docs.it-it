---
title: Test con il proprio copie di test - Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
caps.latest.revision: 4
ms.openlocfilehash: 4e2511f92f9bb034ab468dd030ef238e325ce7c0
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "39121335"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="e746c-102">Test con il proprio copie di test</span><span class="sxs-lookup"><span data-stu-id="e746c-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="e746c-103">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="e746c-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e746c-104">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="e746c-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e746c-105">Quando si scrivono test per l'applicazione è spesso preferibile evitare di raggiungere il database.</span><span class="sxs-lookup"><span data-stu-id="e746c-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="e746c-106">Entity Framework consente di ottenere questo risultato mediante la creazione di un contesto: con comportamento definito per il test, che usa i dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="e746c-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="e746c-107">Opzioni per la creazione di copie di test</span><span class="sxs-lookup"><span data-stu-id="e746c-107">Options for creating test doubles</span></span>  

<span data-ttu-id="e746c-108">Esistono due diversi approcci che possono essere utilizzati per creare una versione in memoria del contesto.</span><span class="sxs-lookup"><span data-stu-id="e746c-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="e746c-109">**Creare il proprio test Double** – questo approccio richiede di scrivere la propria implementazione in memoria del contesto e DbSet.</span><span class="sxs-lookup"><span data-stu-id="e746c-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="e746c-110">Ciò offre un notevole controllo sul modo in cui le classi si comportano ma possono comportare la scrittura e proprietario di una quantità ragionevole di codice.</span><span class="sxs-lookup"><span data-stu-id="e746c-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="e746c-111">**Usare un framework di simulazione per creare copie di test** – usando un framework di simulazione (ad esempio Moq) è possibile avere le implementazioni in memoria del contesto e i set creati dinamicamente in fase di esecuzione per l'utente.</span><span class="sxs-lookup"><span data-stu-id="e746c-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="e746c-112">Questo articolo gestirà la creazione di propri test double.</span><span class="sxs-lookup"><span data-stu-id="e746c-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="e746c-113">Per informazioni sull'uso di un framework di simulazione, vedere [test con un Framework di simulazione](mocking.md).</span><span class="sxs-lookup"><span data-stu-id="e746c-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="e746c-114">Test con le versioni di pre-EF6</span><span class="sxs-lookup"><span data-stu-id="e746c-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="e746c-115">Il codice illustrato in questo articolo è compatibile con Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="e746c-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="e746c-116">Per i test con EF5 e versione precedente, vedere [test con un contesto di Fake](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="e746c-116">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="e746c-117">Limitazioni delle copie di test in memoria di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e746c-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="e746c-118">Copie di test in memoria possono essere un buon metodo per fornire la copertura a livello di bit dell'applicazione che usa Entity Framework di unit test.</span><span class="sxs-lookup"><span data-stu-id="e746c-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="e746c-119">Tuttavia, quando in questo modo si utilizza LINQ to Objects per eseguire query su dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="e746c-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="e746c-120">Ciò può comportare un comportamento diverso rispetto all'uso di provider LINQ di Entity Framework (LINQ to Entities) per tradurre le query SQL che viene eseguita per il database.</span><span class="sxs-lookup"><span data-stu-id="e746c-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="e746c-121">Un esempio di questo tipo una differenza consiste nel caricare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="e746c-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="e746c-122">Se si crea una serie di blog post che ogni sono correlati, quindi quando si usano dati in memoria i post correlati verranno caricati sempre per ogni Blog.</span><span class="sxs-lookup"><span data-stu-id="e746c-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="e746c-123">Tuttavia, durante l'esecuzione di un database i dati solo essere caricati se si usa il metodo Include.</span><span class="sxs-lookup"><span data-stu-id="e746c-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="e746c-124">Per questo motivo, è consigliabile includere sempre un certo livello di test end-to-end (oltre a unit test) per garantire che l'applicazione funzioni correttamente su un database.</span><span class="sxs-lookup"><span data-stu-id="e746c-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="e746c-125">Seguendo insieme a questo articolo</span><span class="sxs-lookup"><span data-stu-id="e746c-125">Following along with this article</span></span>  

<span data-ttu-id="e746c-126">Questo articolo riporta i listati di codice completo che è possibile copiare in Visual Studio per seguire la procedura se si desidera.</span><span class="sxs-lookup"><span data-stu-id="e746c-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="e746c-127">È più semplice creare un **progetto di Unit Test** e sarà necessario alla destinazione **.NET Framework 4.5** per completare le sezioni che usino la modalità asincrona.</span><span class="sxs-lookup"><span data-stu-id="e746c-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="e746c-128">Creazione di un'interfaccia di contesto</span><span class="sxs-lookup"><span data-stu-id="e746c-128">Creating a context interface</span></span>  

<span data-ttu-id="e746c-129">Dobbiamo esaminare test di un servizio che utilizza un Entity Framework model.</span><span class="sxs-lookup"><span data-stu-id="e746c-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="e746c-130">Per poter essere in grado di sostituire il contesto di Entity Framework con una versione in memoria per il test, verranno definiti un'interfaccia che il contesto di Entity Framework (e relativo valore double in memoria) verrà imeplement.</span><span class="sxs-lookup"><span data-stu-id="e746c-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will imeplement.</span></span>  

<span data-ttu-id="e746c-131">Il servizio che si intende testare query e modificare i dati usando le proprietà DbSet del contesto e anche chiamare il metodo SaveChanges per eseguire il push delle modifiche al database.</span><span class="sxs-lookup"><span data-stu-id="e746c-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="e746c-132">Quindi, verrà incluso questi membri sull'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="e746c-132">So we're including these members on the interface.</span></span>  

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

## <a name="the-ef-model"></a><span data-ttu-id="e746c-133">Il modello di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e746c-133">The EF model</span></span>  

<span data-ttu-id="e746c-134">Il servizio dobbiamo testare si avvale di un Entity Framework model costituita dal BloggingContext e le classi di Blog e Post.</span><span class="sxs-lookup"><span data-stu-id="e746c-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="e746c-135">Questo codice potrebbe essere stato generato dalla finestra di progettazione di Entity Framework o da un modello Code First.</span><span class="sxs-lookup"><span data-stu-id="e746c-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="e746c-136">Implementazione dell'interfaccia contesto con Entity Framework Designer</span><span class="sxs-lookup"><span data-stu-id="e746c-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="e746c-137">Si noti che il contesto implementi l'interfaccia IBloggingContext.</span><span class="sxs-lookup"><span data-stu-id="e746c-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="e746c-138">Se si utilizza Code First è possibile modificare il contesto direttamente per implementare l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="e746c-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="e746c-139">Se si usa la finestra di progettazione di Entity Framework è necessario modificare il modello T4 che genera il contesto.</span><span class="sxs-lookup"><span data-stu-id="e746c-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="e746c-140">Aprire il \<model_name\>. File Context.tt è annidato sotto si file edmx, trovare il seguente frammento di codice e aggiungere nell'interfaccia, come illustrato.</span><span class="sxs-lookup"><span data-stu-id="e746c-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="e746c-141">Servizio da sottoporre a test</span><span class="sxs-lookup"><span data-stu-id="e746c-141">Service to be tested</span></span>  

<span data-ttu-id="e746c-142">Per illustrare il test mediante copie di test in memoria si intende scrivere un paio di test per un BlogService.</span><span class="sxs-lookup"><span data-stu-id="e746c-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="e746c-143">Il servizio è in grado di creare nuovi blog (AddBlog) e la restituzione di tutti i blog ordinati per nome (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="e746c-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="e746c-144">Oltre a GetAllBlogs, è stato fornito anche un metodo che verrà visualizzato in modo asincrono tutti i blog ordinati per nome (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="e746c-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

<span data-ttu-id="e746c-145"><a name="creating-the-in-memory-test-doubles"/> # # Creazione di test in memoria raddoppiato</span><span class="sxs-lookup"><span data-stu-id="e746c-145"><a name="creating-the-in-memory-test-doubles"/> ## Creating the in-memory test doubles</span></span>  

<span data-ttu-id="e746c-146">Ora che abbiamo il modello di EF reale e il servizio che può usarle, è possibile creare il test in memoria che è possibile usare per i test double.</span><span class="sxs-lookup"><span data-stu-id="e746c-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="e746c-147">È stato creato un test TestContext double per il contesto.</span><span class="sxs-lookup"><span data-stu-id="e746c-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="e746c-148">In copie di test che è possibile scegliere il comportamento desiderato per supportare i test verrà eseguito.</span><span class="sxs-lookup"><span data-stu-id="e746c-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="e746c-149">In questo esempio si sta semplicemente acquisizione il numero di volte in cui che viene chiamato SaveChanges, ma è possibile includere qualsiasi logica è necessario per verificare lo scenario in cui che si sta testando.</span><span class="sxs-lookup"><span data-stu-id="e746c-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="e746c-150">Abbiamo creato anche un TestDbSet che fornisce un'implementazione in memoria di DbSet.</span><span class="sxs-lookup"><span data-stu-id="e746c-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="e746c-151">È stata fornita un'implementazione completa per tutti i metodi su DbSet (ad eccezione di ricerca), ma è sufficiente implementare i membri che verrà usato lo scenario di test.</span><span class="sxs-lookup"><span data-stu-id="e746c-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="e746c-152">TestDbSet Usa alcune altre classi di infrastruttura che è stato incluso per garantire che le query asincrone possono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="e746c-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

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

### <a name="implementing-find"></a><span data-ttu-id="e746c-153">Implementazione della ricerca</span><span class="sxs-lookup"><span data-stu-id="e746c-153">Implementing Find</span></span>  

<span data-ttu-id="e746c-154">Il metodo Find è difficile da implementare in modo generico.</span><span class="sxs-lookup"><span data-stu-id="e746c-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="e746c-155">Se è necessario testare codice che consente di usare il metodo Find è più semplice creare un test trovare DbSet per ognuno dei tipi di entità devono supportare.</span><span class="sxs-lookup"><span data-stu-id="e746c-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="e746c-156">È quindi possibile scrivere la logica per trovare il particolare tipo di entità, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e746c-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

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

## <a name="writing-some-tests"></a><span data-ttu-id="e746c-157">Scrittura di alcuni test</span><span class="sxs-lookup"><span data-stu-id="e746c-157">Writing some tests</span></span>  

<span data-ttu-id="e746c-158">Questo è tutto che è necessario eseguire per avviare il test.</span><span class="sxs-lookup"><span data-stu-id="e746c-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="e746c-159">Il test seguente crea un oggetto TestContext e quindi un servizio basato su questo contesto.</span><span class="sxs-lookup"><span data-stu-id="e746c-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="e746c-160">Il servizio viene quindi utilizzato per creare un nuovo post di blog: usando il metodo AddBlog.</span><span class="sxs-lookup"><span data-stu-id="e746c-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="e746c-161">Infine, il test verifica che il servizio aggiunto un nuovo post di Blog di proprietà di contesto blog e chiamato SaveChanges su contesto.</span><span class="sxs-lookup"><span data-stu-id="e746c-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="e746c-162">Questo è solo un esempio dei tipi di operazioni che è possibile testare con una copia di test in memoria ed è possibile modificare la logica di copie di test e la verifica per soddisfare i requisiti.</span><span class="sxs-lookup"><span data-stu-id="e746c-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

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

<span data-ttu-id="e746c-163">Ecco un altro esempio di test - questa volta uno che esegue una query.</span><span class="sxs-lookup"><span data-stu-id="e746c-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="e746c-164">Il test viene avviato tramite la creazione di un contesto di test con alcuni dati nella relativa proprietà Blog - si noti che i dati non in ordine alfabetico.</span><span class="sxs-lookup"><span data-stu-id="e746c-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="e746c-165">È quindi possibile creare un BlogService il contesto di test in base e assicurarsi che i dati ottenuti da GetAllBlogs vengono ordinati in base al nome.</span><span class="sxs-lookup"><span data-stu-id="e746c-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

<span data-ttu-id="e746c-166">Infine, verrà scritto un test più che usa il metodo asincrono per assicurarsi che l'infrastruttura di async è incluso nella [TestDbSet](#creating-the-in-memory-test-doubles) funzioni.</span><span class="sxs-lookup"><span data-stu-id="e746c-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

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