---
title: Uso delle transazioni-EF6
description: Utilizzo delle transazioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 15cc2171338defe482767114c58afe16cc1ffe21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073717"
---
# <a name="working-with-transactions"></a>Utilizzo delle transazioni
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Questo documento descrive l'uso delle transazioni in EF6, inclusi i miglioramenti aggiunti da EF5 per semplificare l'utilizzo delle transazioni.  

## <a name="what-ef-does-by-default"></a>Che cosa EF esegue per impostazione predefinita  

In tutte le versioni di Entity Framework, ogni volta che si esegue **SaveChanges ()** per inserire, aggiornare o eliminare nel database, il Framework eseguirà il wrapping dell'operazione in una transazione. Questa transazione dura solo un tempo sufficiente per eseguire l'operazione e quindi viene completata. Quando si esegue un'altra operazione di questo tipo, viene avviata una nuova transazione.  

A partire da EF6 **Database.ExecuteSqlCommand ()** per impostazione predefinita, il comando viene eseguito in una transazione se non ne è già presente uno. Sono disponibili overload di questo metodo che consentono di eseguire l'override di questo comportamento, se lo si desidera. Inoltre, nell'esecuzione EF6 delle stored procedure incluse nel modello tramite API come **ObjectContext.ExecuteFunction ()** esegue la stessa operazione (ad eccezione del fatto che non è possibile eseguire l'override del comportamento predefinito al momento).  

In entrambi i casi, il livello di isolamento della transazione corrisponde al livello di isolamento che il provider di database considera l'impostazione predefinita. Per impostazione predefinita, ad esempio, in SQL Server si tratta di READ COMMITTED.  

Entity Framework non esegue il wrapping delle query in una transazione.  

Questa funzionalità predefinita è adatta per un numero elevato di utenti e, in caso affermativo, non è necessario eseguire alcuna operazione diversa in EF6; è sufficiente scrivere il codice come è sempre stato fatto.  

Tuttavia, alcuni utenti richiedono un maggiore controllo sulle transazioni. questa procedura è illustrata nelle sezioni seguenti.  

## <a name="how-the-apis-work"></a>Funzionamento delle API  

Prima di EF6 Entity Framework insistito sull'apertura della connessione al database stessa (generava un'eccezione se era stata passata una connessione già aperta). Poiché una transazione può essere avviata solo su una connessione aperta, questo significa che l'unico modo in cui un utente può eseguire il wrapping di più operazioni in un'unica transazione è stato usare un [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) o usare la proprietà **ObjectContext. Connection** e iniziare a chiamare **Open ()** e **BeginTransaction ()** direttamente nell'oggetto **EntityConnection** restituito. Inoltre, le chiamate API che hanno contattato il database avrebbero esito negativo se era stata avviata una transazione sulla connessione al database sottostante.  

> [!NOTE]
> La limitazione dell'accettazione solo delle connessioni chiuse è stata rimossa in Entity Framework 6. Per informazioni dettagliate, vedere [gestione della connessione](xref:ef6/fundamentals/connection-management).  

A partire da EF6, il Framework offre ora:  

1. **Database. BeginTransaction ()** : un metodo più semplice per l'avvio e il completamento delle transazioni all'interno di un DbContext esistente, che consente di combinare diverse operazioni all'interno della stessa transazione e quindi di eseguire il commit o il rollback di tutte. Consente inoltre all'utente di specificare più facilmente il livello di isolamento per la transazione.  
2. **Database. UseTransaction ()** : che consente al DbContext di usare una transazione che è stata avviata all'esterno del Entity Framework.  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>Combinazione di più operazioni in un'unica transazione nello stesso contesto  

**Database. BeginTransaction ()** dispone di due sostituzioni: una che accetta un valore [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) esplicito e uno che non accetta argomenti e usa l'oggetto IsolationLevel predefinito del provider di database sottostante. Entrambe le sostituzioni restituiscono un oggetto **DbContextTransaction** che fornisce metodi **commit ()** e **rollback ()** che eseguono il commit e il rollback della transazione di archivio sottostante.  

La **DbContextTransaction** deve essere eliminata una volta eseguito il commit o il rollback. Un modo semplice per eseguire questa operazione è l' **uso di (...) {...}** sintassi che chiamerà automaticamente **Dispose ()** al completamento del blocco using:  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> Per iniziare una transazione è necessario che sia aperta la connessione all'archivio sottostante. Quindi, chiamando database. BeginTransaction (), la connessione verrà aperta se non è già aperta. Se DbContextTransaction ha aperto la connessione, verrà chiusa quando viene chiamato il metodo Dispose ().  

### <a name="passing-an-existing-transaction-to-the-context"></a>Passaggio di una transazione esistente al contesto  

A volte si desidera una transazione che sia ancora più ampia nell'ambito e che includa le operazioni sullo stesso database ma al di fuori di EF completamente. A tale scopo, è necessario aprire la connessione e avviare la transazione manualmente, quindi indicare a EF a) di utilizzare la connessione al database già aperta e b) per utilizzare la transazione esistente sulla connessione.  

A tale scopo, è necessario definire e usare un costruttore nella classe Context che eredita da uno dei costruttori DbContext che accettano i, un parametro di connessione esistente e II) il valore booleano contextOwnsConnection.  

> [!NOTE]
> Quando viene chiamato in questo scenario, il flag contextOwnsConnection deve essere impostato su false. Questo è importante in quanto informa Entity Framework che non dovrebbe chiudere la connessione al termine dell'operazione (ad esempio, vedere la riga 4 riportata di seguito):  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

Inoltre, è necessario avviare la transazione manualmente, incluso il valore IsolationLevel se si desidera evitare l'impostazione predefinita, e consentire Entity Framework sapere che è già stata avviata una transazione nella connessione (vedere la riga 33 di seguito).  

È quindi possibile eseguire le operazioni di database direttamente in SqlConnection o in DbContext. Tutte queste operazioni vengono eseguite all'interno di una transazione. Si assume la responsabilità di eseguire il commit o il rollback della transazione e di chiamare Dispose () su di essa, nonché di chiudere ed eliminare la connessione al database. Esempio:  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a>Cancellazione della transazione

È possibile passare null a database. UseTransaction () per cancellare la conoscenza Entity Framework della transazione corrente. Se non si esegue questa operazione, non verrà eseguito il commit né il rollback della transazione esistente. utilizzare quindi con cautela e solo se si è certi di cosa si desidera eseguire. Entity Framework  

### <a name="errors-in-usetransaction"></a>Errori in UseTransaction

Si noterà un'eccezione da database. UseTransaction () se si passa una transazione nei casi seguenti:  
- Entity Framework dispone già di una transazione esistente  
- Entity Framework è già in uso in un TransactionScope  
- L'oggetto connessione nella transazione passata è null. Ovvero, la transazione non è associata a una connessione, in genere si tratta di un segno che la transazione è già stata completata  
- L'oggetto connessione nella transazione passata non corrisponde alla connessione del Entity Framework.  

## <a name="using-transactions-with-other-features"></a>Utilizzo di transazioni con altre funzionalità  

Questa sezione illustra in dettaglio il modo in cui le transazioni precedenti interagiscono con:  

- Resilienza della connessione  
- Metodi asincroni  
- Transazioni TransactionScope  

### <a name="connection-resiliency"></a>Resilienza della connessione  

La nuova funzionalità di resilienza della connessione non funziona con le transazioni avviate dall'utente. Per informazioni dettagliate, vedere ripetizione delle [strategie di esecuzione](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).  

### <a name="asynchronous-programming"></a>Programmazione asincrona  

L'approccio descritto nelle sezioni precedenti non necessita di altre opzioni o impostazioni per lavorare con i metodi di [query e salvataggio asincroni](xref:ef6/fundamentals/async
). Tenere tuttavia presente che, a seconda delle operazioni eseguite all'interno dei metodi asincroni, è possibile che si verifichino transazioni con esecuzione prolungata, che possono a sua volta causare deadlock o un blocco negativo per le prestazioni dell'applicazione complessiva.  

### <a name="transactionscope-transactions"></a>Transazioni TransactionScope  

Prima di EF6 il metodo consigliato per fornire transazioni con ambito più ampio consisteva nell'usare un oggetto TransactionScope:  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

SqlConnection e Entity Framework utilizzeranno entrambe la transazione TransactionScope di ambiente e pertanto verranno sottoposte a commit insieme.  

A partire da .NET 4.5.1 TransactionScope è stato aggiornato in modo da funzionare anche con i metodi asincroni tramite l'uso dell'enumerazione [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) :  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

Esistono tuttavia alcune limitazioni all'approccio TransactionScope:  

- Per usare i metodi asincroni, è necessario .NET 4.5.1 o versione successiva.  
- Non può essere usato in scenari cloud, a meno che non si sia certi di avere una sola connessione (gli scenari cloud non supportano le transazioni distribuite).  
- Non può essere combinato con l'approccio database. UseTransaction () delle sezioni precedenti.  
- Le eccezioni vengono generate se si inviano istruzioni DDL e non sono state abilitate transazioni distribuite tramite il servizio MSDTC.  

Vantaggi dell'approccio TransactionScope:  

- Verrà automaticamente aggiornata una transazione locale a una transazione distribuita se si effettuano più connessioni a un determinato database o si combina una connessione a un database con una connessione a un database diverso all'interno della stessa transazione. Nota: è necessario che il servizio MSDTC sia configurato per consentire il funzionamento delle transazioni distribuite.  
- Semplicità di codifica. Se si preferisce che la transazione sia in ambiente e gestita in modo implicito in background, anziché in modo esplicito sotto il controllo, l'approccio TransactionScope può essere più adatto.  

In breve, con le nuove API database. BeginTransaction () e database. UseTransaction (), l'approccio TransactionScope non è più necessario per la maggior parte degli utenti. Se si continua a usare TransactionScope, tenere presente le limitazioni sopra riportate. È consigliabile usare invece l'approccio descritto nelle sezioni precedenti, laddove possibile.  
