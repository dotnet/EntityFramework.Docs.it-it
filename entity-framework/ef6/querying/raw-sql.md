---
title: Query SQL non elaborate-EF6
description: Query SQL non elaborate in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: da813ede818b24a5e7930202bfa761d65e4e6b72
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064718"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="b8fc6-103">Query SQL non elaborate (EF6)</span><span class="sxs-lookup"><span data-stu-id="b8fc6-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="b8fc6-104">Entity Framework consente di eseguire una query utilizzando LINQ con le classi di entità.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="b8fc6-105">Tuttavia, in alcuni casi è possibile che si desideri eseguire query utilizzando SQL RAW direttamente sul database.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="b8fc6-106">Ciò include la chiamata alle stored procedure, che può essere utile per i modelli di Code First che attualmente non supportano il mapping alle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="b8fc6-107">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="b8fc6-108">Scrittura di query SQL per le entità</span><span class="sxs-lookup"><span data-stu-id="b8fc6-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="b8fc6-109">Il metodo sqlQuery su DbSet consente di scrivere una query SQL non elaborata che restituirà istanze di entità.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="b8fc6-110">Gli oggetti restituiti verranno rilevati dal contesto esattamente come se fossero restituiti da una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="b8fc6-111">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8fc6-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="b8fc6-112">Si noti che, come per le query LINQ, la query non viene eseguita finché non vengono enumerati i risultati. nell'esempio precedente questa operazione viene eseguita con la chiamata a ToList.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="b8fc6-113">Prestare attenzione quando le query SQL non elaborate vengono scritte per due motivi.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="b8fc6-114">In primo luogo, la query deve essere scritta per assicurarsi che restituisca solo entità effettivamente del tipo richiesto.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="b8fc6-115">Ad esempio, quando si utilizzano funzionalità come l'ereditarietà, è facile scrivere una query che creerà entità di tipo CLR errato.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="b8fc6-116">In secondo luogo, alcuni tipi di query SQL non elaborata espongono potenziali rischi per la sicurezza, in particolare per gli attacchi SQL injection.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="b8fc6-117">Assicurarsi di usare i parametri nella query nel modo corretto per proteggersi da tali attacchi.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="b8fc6-118">Caricamento di entità da stored procedure</span><span class="sxs-lookup"><span data-stu-id="b8fc6-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="b8fc6-119">È possibile utilizzare DbSet. sqlQuery per caricare entità dai risultati di un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="b8fc6-120">Il codice seguente, ad esempio, chiama dbo. Procedura getblogs nel database:</span><span class="sxs-lookup"><span data-stu-id="b8fc6-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="b8fc6-121">È anche possibile passare parametri a un stored procedure usando la sintassi seguente:</span><span class="sxs-lookup"><span data-stu-id="b8fc6-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="b8fc6-122">Scrittura di query SQL per tipi non di entità</span><span class="sxs-lookup"><span data-stu-id="b8fc6-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="b8fc6-123">Una query SQL che restituisce istanze di qualsiasi tipo, inclusi i tipi primitivi, può essere creata usando il metodo sqlQuery sulla classe database.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="b8fc6-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8fc6-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="b8fc6-125">I risultati restituiti da sqlQuery sul database non verranno mai rilevati dal contesto anche se gli oggetti sono istanze di un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="b8fc6-126">Invio di comandi non elaborati al database</span><span class="sxs-lookup"><span data-stu-id="b8fc6-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="b8fc6-127">I comandi non di query possono essere inviati al database usando il metodo ExecuteSqlCommand nel database.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="b8fc6-128">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8fc6-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="b8fc6-129">Si noti che tutte le modifiche apportate ai dati nel database tramite ExecuteSqlCommand sono opache al contesto fino a quando le entità non vengono caricate o ricaricate dal database.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="b8fc6-130">Parametri di output</span><span class="sxs-lookup"><span data-stu-id="b8fc6-130">Output Parameters</span></span>  

<span data-ttu-id="b8fc6-131">Se vengono utilizzati parametri di output, i relativi valori non saranno disponibili fino a quando i risultati non sono stati letti completamente.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="b8fc6-132">A causa del comportamento sottostante di DbDataReader, vedere Recupero di [dati tramite un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) per altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="b8fc6-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
