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
# <a name="connection-management"></a>Gestione delle connessioni
Questa pagina descrive il comportamento di Entity Framework per quanto riguarda il passaggio delle connessioni al contesto e la funzionalità dell'API **database. Connection. Open ()** .  

## <a name="passing-connections-to-the-context"></a>Passaggio di connessioni al contesto  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportamento per EF5 e versioni precedenti  

Sono disponibili due costruttori che accettano le connessioni:  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

È possibile usarli, ma è necessario aggirare alcune limitazioni:  

1. Se si passa una connessione aperta a una di queste, la prima volta che il Framework tenta di usarla, viene generata un'eccezione InvalidOperationException che indica che non è possibile aprire nuovamente una connessione già aperta.  
2. Il flag contextOwnsConnection viene interpretato per indicare se la connessione all'archivio sottostante deve essere eliminata quando viene eliminato il contesto. Tuttavia, indipendentemente da questa impostazione, la connessione all'archivio viene sempre chiusa quando il contesto viene eliminato. Quindi, se si dispone di più DbContext con la stessa connessione, il contesto eliminato per primo chiude la connessione. in modo analogo, se è stata mista una connessione ADO.NET esistente con un DbContext, DbContext chiuderà sempre la connessione quando viene eliminata.  

È possibile aggirare la prima limitazione precedente passando una connessione chiusa ed eseguendo solo il codice che la aprirebbe una volta creati tutti i contesti:  

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

Il secondo limite significa semplicemente che è necessario evitare di eliminare tutti gli oggetti DbContext fino a quando non si è pronti per la chiusura della connessione.  

### <a name="behavior-in-ef6-and-future-versions"></a>Comportamento in EF6 e nelle versioni future  

In EF6 e nelle versioni future il DbContext ha gli stessi due costruttori, ma non richiede più che la connessione passata al costruttore venga chiusa quando viene ricevuta. Ora è possibile:  

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

Inoltre, il flag contextOwnsConnection controlla ora se la connessione è stata chiusa ed eliminata quando il DbContext viene eliminato. Nell'esempio precedente la connessione non viene chiusa quando il contesto viene eliminato (riga 32) come sarebbe stato nelle versioni precedenti di EF, ma piuttosto quando la connessione viene eliminata (riga 40).  

Naturalmente, è comunque possibile che il DbContext prenda il controllo della connessione (è sufficiente impostare contextOwnsConnection su true o usare uno degli altri costruttori) se lo si desidera.  

> [!NOTE]
> Quando si utilizzano transazioni con questo nuovo modello, è necessario tenere presenti alcune considerazioni aggiuntive. Per informazioni dettagliate, vedere [utilizzo delle transazioni](xref:ef6/saving/transactions).  

## <a name="databaseconnectionopen"></a>Database. Connection. Open ()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportamento per EF5 e versioni precedenti  

In EF5 e versioni precedenti è presente un bug che indica che **ObjectContext. Connection. state** non è stato aggiornato per riflettere lo stato effettivo della connessione all'archivio sottostante. Se, ad esempio, è stato eseguito il codice seguente, è possibile restituire lo stato **chiuso** anche se in realtà la connessione all'archivio sottostante è **aperta**.  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

Separatamente, se si apre la connessione al database chiamando il database. Connection. Open () verrà aperto fino alla successiva esecuzione di una query o alla chiamata di qualsiasi elemento che richiede una connessione al database (ad esempio, SaveChanges ()) ma dopo la chiusura della connessione all'archivio sottostante. Il contesto riaprirà e richiuderà la connessione ogni volta che è necessario eseguire un'altra operazione di database:  

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

### <a name="behavior-in-ef6-and-future-versions"></a>Comportamento in EF6 e nelle versioni future  

Per EF6 e le versioni future abbiamo adottato l'approccio che se il codice chiamante sceglie di aprire la connessione chiamando context. Database. Connection. Open () ha un buon motivo per eseguire questa operazione e il Framework presuppone che desideri controllare l'apertura e la chiusura della connessione e non chiuderà la connessione automaticamente.  

> [!NOTE]
> Questo può potenzialmente causare connessioni aperte da molto tempo, quindi usare con cautela.  

È stato anche aggiornato il codice in modo che ObjectContext. Connection. state ora tenga traccia dello stato della connessione sottostante correttamente.  

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
