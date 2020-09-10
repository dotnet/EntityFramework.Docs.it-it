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
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="1c8fe-103">Test con un Framework fittizio</span><span class="sxs-lookup"><span data-stu-id="1c8fe-103">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="1c8fe-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1c8fe-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1c8fe-106">Quando si scrivono test per l'applicazione, è spesso consigliabile evitare di raggiungere il database.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="1c8fe-107">Entity Framework consente di ottenere questo risultato creando un contesto, con il comportamento definito dai test, che usa i dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="1c8fe-108">Opzioni per la creazione di doppi di test</span><span class="sxs-lookup"><span data-stu-id="1c8fe-108">Options for creating test doubles</span></span>  

<span data-ttu-id="1c8fe-109">Esistono due approcci diversi che possono essere usati per creare una versione in memoria del contesto.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="1c8fe-110">**Creare doppi test personalizzati** : questo approccio prevede la scrittura di un'implementazione in memoria personalizzata del contesto e di DbSet.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="1c8fe-111">In questo modo è possibile controllare in che modo le classi si comportano, ma possono coinvolgere la scrittura e la creazione di una quantità ragionevole di codice.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="1c8fe-112">**Usare un Framework fittizio per creare doppi di test** : usando un Framework fittizio, ad esempio MOQ, è possibile avere le implementazioni in memoria del contesto e i set creati dinamicamente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="1c8fe-113">In questo articolo viene affrontato l'uso di un Framework fittizio.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-113">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="1c8fe-114">Per creare doppi test personalizzati, vedere [test con i doppi test personalizzati](xref:ef6/fundamentals/testing/writing-test-doubles).</span><span class="sxs-lookup"><span data-stu-id="1c8fe-114">For creating your own test doubles see [Testing with Your Own Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span></span>  

<span data-ttu-id="1c8fe-115">Per illustrare l'uso di EF con un Framework fittizio, verrà usato MOQ.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-115">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="1c8fe-116">Il modo più semplice per ottenere MOQ consiste nell'installare il [pacchetto MOQ da NuGet](https://nuget.org/packages/Moq/).</span><span class="sxs-lookup"><span data-stu-id="1c8fe-116">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="1c8fe-117">Test con versioni precedenti a EF6</span><span class="sxs-lookup"><span data-stu-id="1c8fe-117">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="1c8fe-118">Lo scenario illustrato in questo articolo dipende da alcune modifiche apportate a DbSet in EF6.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-118">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="1c8fe-119">Per i test con EF5 e versioni precedenti, vedere [test con un contesto fittizio](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="1c8fe-119">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="1c8fe-120">Limitazioni dei duplicati del test EF in memoria</span><span class="sxs-lookup"><span data-stu-id="1c8fe-120">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="1c8fe-121">I doppi test in memoria possono essere un metodo efficace per fornire unit test copertura dei bit dell'applicazione che usano EF.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-121">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="1c8fe-122">Tuttavia, quando si esegue questa operazione si utilizza LINQ to Objects per eseguire query sui dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-122">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="1c8fe-123">Questo può comportare un comportamento diverso rispetto all'uso del provider LINQ di EF (LINQ to Entities) per tradurre le query in SQL che viene eseguito sul database.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-123">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="1c8fe-124">Un esempio di tale differenza è il caricamento dei dati correlati.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-124">One example of such a difference is loading related data.</span></span> <span data-ttu-id="1c8fe-125">Se si crea una serie di Blog con post correlati, quando si usano i dati in memoria i post correlati verranno sempre caricati per ogni blog.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-125">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="1c8fe-126">Tuttavia, quando si esegue su un database, i dati verranno caricati solo se si usa il metodo include.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-126">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="1c8fe-127">Per questo motivo, è consigliabile includere sempre un certo livello di test end-to-end (oltre agli unit test) per garantire il corretto funzionamento dell'applicazione in un database.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-127">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="1c8fe-128">Segue con questo articolo</span><span class="sxs-lookup"><span data-stu-id="1c8fe-128">Following along with this article</span></span>  

<span data-ttu-id="1c8fe-129">Questo articolo fornisce elenchi di codici completi che è possibile copiare in Visual Studio per proseguire, se lo si desidera.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-129">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="1c8fe-130">È più semplice creare un **progetto di unit test** ed è necessario specificare come destinazione **.NET Framework 4,5** per completare le sezioni che usano Async.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-130">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="1c8fe-131">Modello EF</span><span class="sxs-lookup"><span data-stu-id="1c8fe-131">The EF model</span></span>  

<span data-ttu-id="1c8fe-132">Il servizio che verrà testato usa un modello EF costituito da BloggingContext e dalle classi post e Blog.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-132">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="1c8fe-133">Questo codice potrebbe essere stato generato dalla finestra di progettazione di EF o essere un modello di Code First.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-133">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="1c8fe-134">Proprietà di DbSet virtuali con la finestra di progettazione EF</span><span class="sxs-lookup"><span data-stu-id="1c8fe-134">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="1c8fe-135">Si noti che le proprietà DbSet nel contesto sono contrassegnate come virtuali.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-135">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="1c8fe-136">Questo consentirà al Framework fittizio di derivare dal contesto ed eseguire l'override di queste proprietà con un'implementazione fittizia.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-136">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="1c8fe-137">Se si usa Code First, è possibile modificare direttamente le classi.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-137">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="1c8fe-138">Se si usa la finestra di progettazione EF, sarà necessario modificare il modello T4 che genera il contesto.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-138">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="1c8fe-139">Aprire il \<model_name\> . File Context.tt annidato sotto il file edmx, trovare il frammento di codice seguente e aggiungere la parola chiave virtual come illustrato.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-139">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="1c8fe-140">Servizio da testare</span><span class="sxs-lookup"><span data-stu-id="1c8fe-140">Service to be tested</span></span>  

<span data-ttu-id="1c8fe-141">Per illustrare i test con i doppi test in memoria, verranno scritti due test per un BlogService.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-141">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="1c8fe-142">Il servizio è in grado di creare nuovi Blog (AddBlog) e di restituire tutti i Blog ordinati in base al nome (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="1c8fe-142">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="1c8fe-143">Oltre a GetAllBlogs, è stato inoltre fornito un metodo che consente di ottenere in modo asincrono tutti i Blog ordinati in base al nome (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="1c8fe-143">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="1c8fe-144">Test di scenari non di query</span><span class="sxs-lookup"><span data-stu-id="1c8fe-144">Testing non-query scenarios</span></span>  

<span data-ttu-id="1c8fe-145">Questo è tutto ciò che è necessario fare per iniziare a testare i metodi non query.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-145">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="1c8fe-146">Il test seguente usa MOQ per creare un contesto.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-146">The following test uses Moq to create a context.</span></span> <span data-ttu-id="1c8fe-147">Crea quindi un DbSet \<Blog\> e lo collega per essere restituito dalla proprietà Blogs del contesto.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-147">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="1c8fe-148">Successivamente, il contesto viene usato per creare un nuovo BlogService che viene quindi usato per creare un nuovo Blog, usando il metodo AddBlog.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-148">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="1c8fe-149">Infine, il test verifica che il servizio abbia aggiunto un nuovo blog e abbia chiamato SaveChanges nel contesto.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-149">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="1c8fe-150">Test di scenari di query</span><span class="sxs-lookup"><span data-stu-id="1c8fe-150">Testing query scenarios</span></span>  

<span data-ttu-id="1c8fe-151">Per poter eseguire query sul doppio del test di DbSet, è necessario configurare un'implementazione di IQueryable.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-151">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="1c8fe-152">Il primo passaggio consiste nel creare alcuni dati in memoria, ovvero l'uso di un elenco \<Blog\> .</span><span class="sxs-lookup"><span data-stu-id="1c8fe-152">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="1c8fe-153">Successivamente, creiamo un contesto e DBSet \<Blog\> quindi l'implementazione di IQueryable per DBSet, che delegano solo al provider LINQ to Objects che funziona con List \<T\> .</span><span class="sxs-lookup"><span data-stu-id="1c8fe-153">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="1c8fe-154">È quindi possibile creare un BlogService in base ai doppi test e verificare che i dati restituiti da GetAllBlogs siano ordinati in base al nome.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-154">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="1c8fe-155">Test con query asincrone</span><span class="sxs-lookup"><span data-stu-id="1c8fe-155">Testing with async queries</span></span>

<span data-ttu-id="1c8fe-156">In Entity Framework 6 è stato introdotto un set di metodi di estensione che è possibile utilizzare per eseguire una query in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-156">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="1c8fe-157">Esempi di questi metodi includono ToListAsync, FirstAsync, ForEachAsync e così via.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-157">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="1c8fe-158">Poiché Entity Framework query utilizzano LINQ, i metodi di estensione sono definiti in IQueryable e IEnumerable.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-158">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="1c8fe-159">Tuttavia, poiché sono progettati solo per essere utilizzati con Entity Framework è possibile che venga visualizzato l'errore seguente se si tenta di utilizzarli in una query LINQ che non è una query Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="1c8fe-159">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="1c8fe-160">L'oggetto IQueryable di origine non implementa IDbAsyncEnumerable {0} .</span><span class="sxs-lookup"><span data-stu-id="1c8fe-160">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="1c8fe-161">Solo le origini che implementano IDbAsyncEnumerable possono essere usate per Entity Framework operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-161">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="1c8fe-162">Per ulteriori informazioni [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) , vedere.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-162">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="1c8fe-163">Mentre i metodi asincroni sono supportati solo quando si esegue su una query EF, è consigliabile usarli nel unit test quando vengono eseguiti su un test in memoria Double di un DbSet.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-163">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="1c8fe-164">Per usare i metodi asincroni è necessario creare un DbAsyncQueryProvider in memoria per elaborare la query asincrona.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-164">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="1c8fe-165">Sebbene sia possibile configurare un provider di query usando MOQ, è molto più semplice creare un'implementazione di test doppia nel codice.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-165">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="1c8fe-166">Il codice per questa implementazione è il seguente:</span><span class="sxs-lookup"><span data-stu-id="1c8fe-166">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="1c8fe-167">Ora che è disponibile un provider di query asincrono, è possibile scrivere un unit test per il nuovo metodo GetAllBlogsAsync.</span><span class="sxs-lookup"><span data-stu-id="1c8fe-167">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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
