---
title: Gestione della connessione-EF6
description: Gestione delle connessioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: c352e761a9891b5c275f32752f10de13222bf48e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617220"
---
# <a name="connection-management"></a><span data-ttu-id="92122-103">Gestione delle connessioni</span><span class="sxs-lookup"><span data-stu-id="92122-103">Connection management</span></span>
<span data-ttu-id="92122-104">Questa pagina descrive il comportamento di Entity Framework per quanto riguarda il passaggio delle connessioni al contesto e la funzionalità dell'API **database. Connection. Open ()** .</span><span class="sxs-lookup"><span data-stu-id="92122-104">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="92122-105">Passaggio di connessioni al contesto</span><span class="sxs-lookup"><span data-stu-id="92122-105">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="92122-106">Comportamento per EF5 e versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="92122-106">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="92122-107">Sono disponibili due costruttori che accettano le connessioni:</span><span class="sxs-lookup"><span data-stu-id="92122-107">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="92122-108">È possibile usarli, ma è necessario aggirare alcune limitazioni:</span><span class="sxs-lookup"><span data-stu-id="92122-108">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="92122-109">Se si passa una connessione aperta a una di queste, la prima volta che il Framework tenta di usarla, viene generata un'eccezione InvalidOperationException che indica che non è possibile aprire nuovamente una connessione già aperta.</span><span class="sxs-lookup"><span data-stu-id="92122-109">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="92122-110">Il flag contextOwnsConnection viene interpretato per indicare se la connessione all'archivio sottostante deve essere eliminata quando viene eliminato il contesto.</span><span class="sxs-lookup"><span data-stu-id="92122-110">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="92122-111">Tuttavia, indipendentemente da questa impostazione, la connessione all'archivio viene sempre chiusa quando il contesto viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="92122-111">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="92122-112">Quindi, se si dispone di più DbContext con la stessa connessione, il contesto eliminato per primo chiude la connessione. in modo analogo, se è stata mista una connessione ADO.NET esistente con un DbContext, DbContext chiuderà sempre la connessione quando viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="92122-112">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="92122-113">È possibile aggirare la prima limitazione precedente passando una connessione chiusa ed eseguendo solo il codice che la aprirebbe una volta creati tutti i contesti:</span><span class="sxs-lookup"><span data-stu-id="92122-113">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

<span data-ttu-id="92122-114">Il secondo limite significa semplicemente che è necessario evitare di eliminare tutti gli oggetti DbContext fino a quando non si è pronti per la chiusura della connessione.</span><span class="sxs-lookup"><span data-stu-id="92122-114">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="92122-115">Comportamento in EF6 e nelle versioni future</span><span class="sxs-lookup"><span data-stu-id="92122-115">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="92122-116">In EF6 e nelle versioni future il DbContext ha gli stessi due costruttori, ma non richiede più che la connessione passata al costruttore venga chiusa quando viene ricevuta.</span><span class="sxs-lookup"><span data-stu-id="92122-116">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="92122-117">Ora è possibile:</span><span class="sxs-lookup"><span data-stu-id="92122-117">So this is now possible:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

<span data-ttu-id="92122-118">Inoltre, il flag contextOwnsConnection controlla ora se la connessione è stata chiusa ed eliminata quando il DbContext viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="92122-118">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="92122-119">Nell'esempio precedente la connessione non viene chiusa quando il contesto viene eliminato (riga 32) come sarebbe stato nelle versioni precedenti di EF, ma piuttosto quando la connessione viene eliminata (riga 40).</span><span class="sxs-lookup"><span data-stu-id="92122-119">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="92122-120">Naturalmente, è comunque possibile che il DbContext prenda il controllo della connessione (è sufficiente impostare contextOwnsConnection su true o usare uno degli altri costruttori) se lo si desidera.</span><span class="sxs-lookup"><span data-stu-id="92122-120">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="92122-121">Quando si utilizzano transazioni con questo nuovo modello, è necessario tenere presenti alcune considerazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="92122-121">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="92122-122">Per informazioni dettagliate, vedere [utilizzo delle transazioni](xref:ef6/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="92122-122">For details see [Working with Transactions](xref:ef6/saving/transactions).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="92122-123">Database. Connection. Open ()</span><span class="sxs-lookup"><span data-stu-id="92122-123">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="92122-124">Comportamento per EF5 e versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="92122-124">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="92122-125">In EF5 e versioni precedenti è presente un bug che indica che **ObjectContext. Connection. state** non è stato aggiornato per riflettere lo stato effettivo della connessione all'archivio sottostante.</span><span class="sxs-lookup"><span data-stu-id="92122-125">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="92122-126">Se, ad esempio, è stato eseguito il codice seguente, è possibile restituire lo stato **chiuso** anche se in realtà la connessione all'archivio sottostante è **aperta**.</span><span class="sxs-lookup"><span data-stu-id="92122-126">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="92122-127">Separatamente, se si apre la connessione al database chiamando il database. Connection. Open () verrà aperto fino alla successiva esecuzione di una query o alla chiamata di qualsiasi elemento che richiede una connessione al database (ad esempio, SaveChanges ()) ma dopo la chiusura della connessione all'archivio sottostante.</span><span class="sxs-lookup"><span data-stu-id="92122-127">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="92122-128">Il contesto riaprirà e richiuderà la connessione ogni volta che è necessario eseguire un'altra operazione di database:</span><span class="sxs-lookup"><span data-stu-id="92122-128">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="92122-129">Comportamento in EF6 e nelle versioni future</span><span class="sxs-lookup"><span data-stu-id="92122-129">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="92122-130">Per EF6 e le versioni future abbiamo adottato l'approccio che se il codice chiamante sceglie di aprire la connessione chiamando context. Database. Connection. Open () ha un buon motivo per eseguire questa operazione e il Framework presuppone che desideri controllare l'apertura e la chiusura della connessione e non chiuderà la connessione automaticamente.</span><span class="sxs-lookup"><span data-stu-id="92122-130">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="92122-131">Questo può potenzialmente causare connessioni aperte da molto tempo, quindi usare con cautela.</span><span class="sxs-lookup"><span data-stu-id="92122-131">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="92122-132">È stato anche aggiornato il codice in modo che ObjectContext. Connection. state ora tenga traccia dello stato della connessione sottostante correttamente.</span><span class="sxs-lookup"><span data-stu-id="92122-132">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
