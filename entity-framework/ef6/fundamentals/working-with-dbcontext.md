---
title: Uso di DbContext-EF6
description: Utilizzo di DbContext in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: aa980e102862b559c8f38418cf90a11f284cc48c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062802"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="42497-103">Uso di DbContext</span><span class="sxs-lookup"><span data-stu-id="42497-103">Working with DbContext</span></span>

<span data-ttu-id="42497-104">Per utilizzare Entity Framework per eseguire query, inserire, aggiornare ed eliminare dati utilizzando oggetti .NET, è innanzitutto necessario [creare un modello](xref:ef6/modeling/index) che esegue il mapping delle entità e delle relazioni definite nel modello alle tabelle di un database.</span><span class="sxs-lookup"><span data-stu-id="42497-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="42497-105">Una volta ottenuto un modello, la classe primaria con cui interagisce l'applicazione è `System.Data.Entity.DbContext` (spesso definita classe del contesto).</span><span class="sxs-lookup"><span data-stu-id="42497-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="42497-106">È possibile usare un DbContext associato a un modello per:</span><span class="sxs-lookup"><span data-stu-id="42497-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="42497-107">Scrivere ed eseguire query</span><span class="sxs-lookup"><span data-stu-id="42497-107">Write and execute queries</span></span>   
- <span data-ttu-id="42497-108">Materializzare i risultati della query come oggetti entità</span><span class="sxs-lookup"><span data-stu-id="42497-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="42497-109">Rilevare le modifiche apportate a tali oggetti</span><span class="sxs-lookup"><span data-stu-id="42497-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="42497-110">Mantieni nuovamente le modifiche all'oggetto nel database</span><span class="sxs-lookup"><span data-stu-id="42497-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="42497-111">Associare oggetti in memoria ai controlli dell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="42497-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="42497-112">In questa pagina vengono fornite alcune indicazioni su come gestire la classe del contesto.</span><span class="sxs-lookup"><span data-stu-id="42497-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="42497-113">Definizione di una classe derivata DbContext</span><span class="sxs-lookup"><span data-stu-id="42497-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="42497-114">Il metodo consigliato per lavorare con context consiste nel definire una classe che deriva da DbContext ed espone le proprietà DbSet che rappresentano le raccolte delle entità specificate nel contesto.</span><span class="sxs-lookup"><span data-stu-id="42497-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="42497-115">Se si usa la finestra di progettazione EF, il contesto verrà generato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="42497-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="42497-116">Se si lavora con Code First, in genere si scriverà il contesto autonomamente.</span><span class="sxs-lookup"><span data-stu-id="42497-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="42497-117">Quando si dispone di un contesto, è necessario eseguire una query per, aggiungere (usando `Add` `Attach` i metodi o) o rimuovere (usando `Remove` ) entità nel contesto tramite queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="42497-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="42497-118">L'accesso a una `DbSet` Proprietà in un oggetto Context rappresenta una query iniziale che restituisce tutte le entità del tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="42497-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="42497-119">Si noti che solo l'accesso a una proprietà non eseguirà la query.</span><span class="sxs-lookup"><span data-stu-id="42497-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="42497-120">Una query viene eseguita nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="42497-120">A query is executed when:</span></span>  

- <span data-ttu-id="42497-121">Enumerata da un'istruzione `foreach` (C#) o `For Each` (Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="42497-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="42497-122">Viene enumerato da un'operazione di raccolta, ad esempio `ToArray` , `ToDictionary` o `ToList` .</span><span class="sxs-lookup"><span data-stu-id="42497-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="42497-123">Gli operatori LINQ, ad esempio `First` o, `Any` vengono specificati nella parte più esterna della query.</span><span class="sxs-lookup"><span data-stu-id="42497-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="42497-124">Viene chiamato uno dei metodi seguenti: il `Load` metodo di estensione, `DbEntityEntry.Reload` ,  `Database.ExecuteSqlCommand` e `DbSet<T>.Find` , se un'entità con la chiave specificata non viene trovata già caricata nel contesto.</span><span class="sxs-lookup"><span data-stu-id="42497-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="42497-125">Durata</span><span class="sxs-lookup"><span data-stu-id="42497-125">Lifetime</span></span>  

<span data-ttu-id="42497-126">La durata del contesto inizia al momento della creazione dell'istanza e termina quando l'istanza viene eliminata o sottoposta a Garbage Collection.</span><span class="sxs-lookup"><span data-stu-id="42497-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="42497-127">Utilizzare l' **utilizzo** di se si desidera che tutte le risorse che i controlli del contesto vengano eliminate alla fine del blocco.</span><span class="sxs-lookup"><span data-stu-id="42497-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="42497-128">Quando si utilizza **, il**compilatore crea automaticamente un blocco try/finally e chiama Dispose nel blocco **finally** .</span><span class="sxs-lookup"><span data-stu-id="42497-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="42497-129">Di seguito sono riportate alcune linee guida generali per la scelta della durata del contesto:</span><span class="sxs-lookup"><span data-stu-id="42497-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="42497-130">Quando si utilizzano applicazioni Web, utilizzare un'istanza del contesto per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="42497-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="42497-131">Quando si utilizza Windows Presentation Foundation (WPF) o Windows Forms, utilizzare un'istanza del contesto per ogni modulo.</span><span class="sxs-lookup"><span data-stu-id="42497-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="42497-132">In questo modo è possibile utilizzare la funzionalità di rilevamento delle modifiche fornita dal contesto.</span><span class="sxs-lookup"><span data-stu-id="42497-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="42497-133">Se l'istanza del contesto viene creata da un contenitore di inserimento delle dipendenze, in genere è responsabilità del contenitore eliminare il contesto.</span><span class="sxs-lookup"><span data-stu-id="42497-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="42497-134">Se il contesto viene creato nel codice dell'applicazione, ricordarsi di eliminare il contesto quando non è più necessario.</span><span class="sxs-lookup"><span data-stu-id="42497-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="42497-135">Quando si utilizza il contesto con esecuzione prolungata, considerare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="42497-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="42497-136">Quando si caricano più oggetti e i relativi riferimenti in memoria, il consumo di memoria del contesto può aumentare rapidamente.</span><span class="sxs-lookup"><span data-stu-id="42497-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="42497-137">È possibile pertanto che si verifichino problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="42497-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="42497-138">Il contesto non è thread-safe, pertanto non deve essere condiviso tra più thread che vi lavorano contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="42497-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="42497-139">Se un'eccezione fa sì che il contesto sia in uno stato irreversibile, è possibile che l'intera applicazione venga terminata.</span><span class="sxs-lookup"><span data-stu-id="42497-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="42497-140">Le possibilità che si verifichino problemi correlati alla concorrenza aumentano quando l'intervallo tra il momento in cui viene eseguita una query sui dati e quello in cui i dati vengono aggiornati aumenta.</span><span class="sxs-lookup"><span data-stu-id="42497-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="42497-141">Connessioni</span><span class="sxs-lookup"><span data-stu-id="42497-141">Connections</span></span>  

<span data-ttu-id="42497-142">Per impostazione predefinita, il contesto gestisce le connessioni al database.</span><span class="sxs-lookup"><span data-stu-id="42497-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="42497-143">Il contesto si apre e chiude le connessioni in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="42497-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="42497-144">Ad esempio, il contesto apre una connessione per eseguire una query e quindi chiude la connessione quando tutti i set di risultati sono stati elaborati.</span><span class="sxs-lookup"><span data-stu-id="42497-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="42497-145">In alcuni casi, tuttavia, può essere necessario disporre di maggiore controllo sull'apertura e la chiusura della connessione.</span><span class="sxs-lookup"><span data-stu-id="42497-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="42497-146">Quando si utilizza SQL Server Compact, ad esempio, è spesso consigliabile mantenere una connessione aperta separata al database per la durata dell'applicazione per migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="42497-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="42497-147">È possibile gestire manualmente questo processo tramite la proprietà `Connection`.</span><span class="sxs-lookup"><span data-stu-id="42497-147">You can manage this process manually by using the `Connection` property.</span></span>  
