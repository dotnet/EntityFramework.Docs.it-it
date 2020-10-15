---
title: Uso delle transazioni-EF6
description: Utilizzo delle transazioni in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 525b5cf605c1b61225ee2b9f1e0559a8e13f3052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064432"
---
# <a name="working-with-transactions"></a><span data-ttu-id="51adc-103">Utilizzo delle transazioni</span><span class="sxs-lookup"><span data-stu-id="51adc-103">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="51adc-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="51adc-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="51adc-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="51adc-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="51adc-106">Questo documento descrive l'uso delle transazioni in EF6, inclusi i miglioramenti aggiunti da EF5 per semplificare l'utilizzo delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="51adc-106">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="51adc-107">Che cosa EF esegue per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="51adc-107">What EF does by default</span></span>  

<span data-ttu-id="51adc-108">In tutte le versioni di Entity Framework, ogni volta che si esegue **SaveChanges ()** per inserire, aggiornare o eliminare nel database, il Framework eseguirà il wrapping dell'operazione in una transazione.</span><span class="sxs-lookup"><span data-stu-id="51adc-108">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="51adc-109">Questa transazione dura solo un tempo sufficiente per eseguire l'operazione e quindi viene completata.</span><span class="sxs-lookup"><span data-stu-id="51adc-109">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="51adc-110">Quando si esegue un'altra operazione di questo tipo, viene avviata una nuova transazione.</span><span class="sxs-lookup"><span data-stu-id="51adc-110">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="51adc-111">A partire da EF6 **Database.ExecuteSqlCommand ()** per impostazione predefinita, il comando viene eseguito in una transazione se non ne è già presente uno.</span><span class="sxs-lookup"><span data-stu-id="51adc-111">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="51adc-112">Sono disponibili overload di questo metodo che consentono di eseguire l'override di questo comportamento, se lo si desidera.</span><span class="sxs-lookup"><span data-stu-id="51adc-112">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="51adc-113">Inoltre, nell'esecuzione EF6 delle stored procedure incluse nel modello tramite API come **ObjectContext.ExecuteFunction ()** esegue la stessa operazione (ad eccezione del fatto che non è possibile eseguire l'override del comportamento predefinito al momento).</span><span class="sxs-lookup"><span data-stu-id="51adc-113">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="51adc-114">In entrambi i casi, il livello di isolamento della transazione corrisponde al livello di isolamento che il provider di database considera l'impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="51adc-114">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="51adc-115">Per impostazione predefinita, ad esempio, in SQL Server si tratta di READ COMMITTED.</span><span class="sxs-lookup"><span data-stu-id="51adc-115">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="51adc-116">Entity Framework non esegue il wrapping delle query in una transazione.</span><span class="sxs-lookup"><span data-stu-id="51adc-116">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="51adc-117">Questa funzionalità predefinita è adatta per un numero elevato di utenti e, in caso affermativo, non è necessario eseguire alcuna operazione diversa in EF6; è sufficiente scrivere il codice come è sempre stato fatto.</span><span class="sxs-lookup"><span data-stu-id="51adc-117">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="51adc-118">Tuttavia, alcuni utenti richiedono un maggiore controllo sulle transazioni. questa procedura è illustrata nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="51adc-118">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="51adc-119">Funzionamento delle API</span><span class="sxs-lookup"><span data-stu-id="51adc-119">How the APIs work</span></span>  

<span data-ttu-id="51adc-120">Prima di EF6 Entity Framework insistito sull'apertura della connessione al database stessa (generava un'eccezione se era stata passata una connessione già aperta).</span><span class="sxs-lookup"><span data-stu-id="51adc-120">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="51adc-121">Poiché una transazione può essere avviata solo su una connessione aperta, questo significa che l'unico modo in cui un utente può eseguire il wrapping di più operazioni in un'unica transazione è stato usare un [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) o usare la proprietà **ObjectContext. Connection** e iniziare a chiamare **Open ()** e **BeginTransaction ()** direttamente nell'oggetto **EntityConnection** restituito.</span><span class="sxs-lookup"><span data-stu-id="51adc-121">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="51adc-122">Inoltre, le chiamate API che hanno contattato il database avrebbero esito negativo se era stata avviata una transazione sulla connessione al database sottostante.</span><span class="sxs-lookup"><span data-stu-id="51adc-122">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="51adc-123">La limitazione dell'accettazione solo delle connessioni chiuse è stata rimossa in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="51adc-123">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="51adc-124">Per informazioni dettagliate, vedere [gestione della connessione](xref:ef6/fundamentals/connection-management).</span><span class="sxs-lookup"><span data-stu-id="51adc-124">For details, see [Connection Management](xref:ef6/fundamentals/connection-management).</span></span>  

<span data-ttu-id="51adc-125">A partire da EF6, il Framework offre ora:</span><span class="sxs-lookup"><span data-stu-id="51adc-125">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="51adc-126">**Database. BeginTransaction ()** : un metodo più semplice per l'avvio e il completamento delle transazioni all'interno di un DbContext esistente, che consente di combinare diverse operazioni all'interno della stessa transazione e quindi di eseguire il commit o il rollback di tutte.</span><span class="sxs-lookup"><span data-stu-id="51adc-126">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="51adc-127">Consente inoltre all'utente di specificare più facilmente il livello di isolamento per la transazione.</span><span class="sxs-lookup"><span data-stu-id="51adc-127">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="51adc-128">**Database. UseTransaction ()** : che consente al DbContext di usare una transazione che è stata avviata all'esterno del Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="51adc-128">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="51adc-129">Combinazione di più operazioni in un'unica transazione nello stesso contesto</span><span class="sxs-lookup"><span data-stu-id="51adc-129">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="51adc-130">**Database. BeginTransaction ()** dispone di due sostituzioni: una che accetta un valore [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) esplicito e uno che non accetta argomenti e usa l'oggetto IsolationLevel predefinito del provider di database sottostante.</span><span class="sxs-lookup"><span data-stu-id="51adc-130">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="51adc-131">Entrambe le sostituzioni restituiscono un oggetto **DbContextTransaction** che fornisce metodi **commit ()** e **rollback ()** che eseguono il commit e il rollback della transazione di archivio sottostante.</span><span class="sxs-lookup"><span data-stu-id="51adc-131">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="51adc-132">La **DbContextTransaction** deve essere eliminata una volta eseguito il commit o il rollback.</span><span class="sxs-lookup"><span data-stu-id="51adc-132">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="51adc-133">Un modo semplice per eseguire questa operazione è l' **uso di (...) {...}**</span><span class="sxs-lookup"><span data-stu-id="51adc-133">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="51adc-134">sintassi che chiamerà automaticamente **Dispose ()** al completamento del blocco using:</span><span class="sxs-lookup"><span data-stu-id="51adc-134">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
> <span data-ttu-id="51adc-135">Per iniziare una transazione è necessario che sia aperta la connessione all'archivio sottostante.</span><span class="sxs-lookup"><span data-stu-id="51adc-135">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="51adc-136">Quindi, chiamando database. BeginTransaction (), la connessione verrà aperta se non è già aperta.</span><span class="sxs-lookup"><span data-stu-id="51adc-136">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="51adc-137">Se DbContextTransaction ha aperto la connessione, verrà chiusa quando viene chiamato il metodo Dispose ().</span><span class="sxs-lookup"><span data-stu-id="51adc-137">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="51adc-138">Passaggio di una transazione esistente al contesto</span><span class="sxs-lookup"><span data-stu-id="51adc-138">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="51adc-139">A volte si desidera una transazione che sia ancora più ampia nell'ambito e che includa le operazioni sullo stesso database ma al di fuori di EF completamente.</span><span class="sxs-lookup"><span data-stu-id="51adc-139">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="51adc-140">A tale scopo, è necessario aprire la connessione e avviare la transazione manualmente, quindi indicare a EF a) di utilizzare la connessione al database già aperta e b) per utilizzare la transazione esistente sulla connessione.</span><span class="sxs-lookup"><span data-stu-id="51adc-140">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="51adc-141">A tale scopo, è necessario definire e usare un costruttore nella classe Context che eredita da uno dei costruttori DbContext che accettano i, un parametro di connessione esistente e II) il valore booleano contextOwnsConnection.</span><span class="sxs-lookup"><span data-stu-id="51adc-141">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="51adc-142">Quando viene chiamato in questo scenario, il flag contextOwnsConnection deve essere impostato su false.</span><span class="sxs-lookup"><span data-stu-id="51adc-142">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="51adc-143">Questo è importante in quanto informa Entity Framework che non dovrebbe chiudere la connessione al termine dell'operazione (ad esempio, vedere la riga 4 riportata di seguito):</span><span class="sxs-lookup"><span data-stu-id="51adc-143">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="51adc-144">Inoltre, è necessario avviare la transazione manualmente, incluso il valore IsolationLevel se si desidera evitare l'impostazione predefinita, e consentire Entity Framework sapere che è già stata avviata una transazione nella connessione (vedere la riga 33 di seguito).</span><span class="sxs-lookup"><span data-stu-id="51adc-144">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="51adc-145">È quindi possibile eseguire le operazioni di database direttamente in SqlConnection o in DbContext.</span><span class="sxs-lookup"><span data-stu-id="51adc-145">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="51adc-146">Tutte queste operazioni vengono eseguite all'interno di una transazione.</span><span class="sxs-lookup"><span data-stu-id="51adc-146">All such operations are executed within one transaction.</span></span> <span data-ttu-id="51adc-147">Si assume la responsabilità di eseguire il commit o il rollback della transazione e di chiamare Dispose () su di essa, nonché di chiudere ed eliminare la connessione al database.</span><span class="sxs-lookup"><span data-stu-id="51adc-147">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="51adc-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="51adc-148">For example:</span></span>  

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

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="51adc-149">Cancellazione della transazione</span><span class="sxs-lookup"><span data-stu-id="51adc-149">Clearing up the transaction</span></span>

<span data-ttu-id="51adc-150">È possibile passare null a database. UseTransaction () per cancellare la conoscenza Entity Framework della transazione corrente.</span><span class="sxs-lookup"><span data-stu-id="51adc-150">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="51adc-151">Se non si esegue questa operazione, non verrà eseguito il commit né il rollback della transazione esistente. utilizzare quindi con cautela e solo se si è certi di cosa si desidera eseguire. Entity Framework</span><span class="sxs-lookup"><span data-stu-id="51adc-151">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="51adc-152">Errori in UseTransaction</span><span class="sxs-lookup"><span data-stu-id="51adc-152">Errors in UseTransaction</span></span>

<span data-ttu-id="51adc-153">Si noterà un'eccezione da database. UseTransaction () se si passa una transazione nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="51adc-153">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="51adc-154">Entity Framework dispone già di una transazione esistente</span><span class="sxs-lookup"><span data-stu-id="51adc-154">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="51adc-155">Entity Framework è già in uso in un TransactionScope</span><span class="sxs-lookup"><span data-stu-id="51adc-155">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="51adc-156">L'oggetto connessione nella transazione passata è null.</span><span class="sxs-lookup"><span data-stu-id="51adc-156">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="51adc-157">Ovvero, la transazione non è associata a una connessione, in genere si tratta di un segno che la transazione è già stata completata</span><span class="sxs-lookup"><span data-stu-id="51adc-157">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="51adc-158">L'oggetto connessione nella transazione passata non corrisponde alla connessione del Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="51adc-158">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="51adc-159">Utilizzo di transazioni con altre funzionalità</span><span class="sxs-lookup"><span data-stu-id="51adc-159">Using transactions with other features</span></span>  

<span data-ttu-id="51adc-160">Questa sezione illustra in dettaglio il modo in cui le transazioni precedenti interagiscono con:</span><span class="sxs-lookup"><span data-stu-id="51adc-160">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="51adc-161">Resilienza delle connessioni</span><span class="sxs-lookup"><span data-stu-id="51adc-161">Connection resiliency</span></span>  
- <span data-ttu-id="51adc-162">Metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="51adc-162">Asynchronous methods</span></span>  
- <span data-ttu-id="51adc-163">Transazioni TransactionScope</span><span class="sxs-lookup"><span data-stu-id="51adc-163">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="51adc-164">Resilienza della connessione</span><span class="sxs-lookup"><span data-stu-id="51adc-164">Connection Resiliency</span></span>  

<span data-ttu-id="51adc-165">La nuova funzionalità di resilienza della connessione non funziona con le transazioni avviate dall'utente.</span><span class="sxs-lookup"><span data-stu-id="51adc-165">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="51adc-166">Per informazioni dettagliate, vedere ripetizione delle [strategie di esecuzione](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span><span class="sxs-lookup"><span data-stu-id="51adc-166">For details, see [Retrying Execution Strategies](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="51adc-167">Programmazione asincrona</span><span class="sxs-lookup"><span data-stu-id="51adc-167">Asynchronous Programming</span></span>  

<span data-ttu-id="51adc-168">L'approccio descritto nelle sezioni precedenti non necessita di altre opzioni o impostazioni per lavorare con i metodi di [query e salvataggio asincroni](xref:ef6/fundamentals/async
).</span><span class="sxs-lookup"><span data-stu-id="51adc-168">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](xref:ef6/fundamentals/async
).</span></span> <span data-ttu-id="51adc-169">Tenere tuttavia presente che, a seconda delle operazioni eseguite all'interno dei metodi asincroni, è possibile che si verifichino transazioni con esecuzione prolungata, che possono a sua volta causare deadlock o un blocco negativo per le prestazioni dell'applicazione complessiva.</span><span class="sxs-lookup"><span data-stu-id="51adc-169">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="51adc-170">Transazioni TransactionScope</span><span class="sxs-lookup"><span data-stu-id="51adc-170">TransactionScope Transactions</span></span>  

<span data-ttu-id="51adc-171">Prima di EF6 il metodo consigliato per fornire transazioni con ambito più ampio consisteva nell'usare un oggetto TransactionScope:</span><span class="sxs-lookup"><span data-stu-id="51adc-171">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="51adc-172">SqlConnection e Entity Framework utilizzeranno entrambe la transazione TransactionScope di ambiente e pertanto verranno sottoposte a commit insieme.</span><span class="sxs-lookup"><span data-stu-id="51adc-172">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="51adc-173">A partire da .NET 4.5.1 TransactionScope è stato aggiornato in modo da funzionare anche con i metodi asincroni tramite l'uso dell'enumerazione [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) :</span><span class="sxs-lookup"><span data-stu-id="51adc-173">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="51adc-174">Esistono tuttavia alcune limitazioni all'approccio TransactionScope:</span><span class="sxs-lookup"><span data-stu-id="51adc-174">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="51adc-175">Per usare i metodi asincroni, è necessario .NET 4.5.1 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="51adc-175">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="51adc-176">Non può essere usato in scenari cloud, a meno che non si sia certi di avere una sola connessione (gli scenari cloud non supportano le transazioni distribuite).</span><span class="sxs-lookup"><span data-stu-id="51adc-176">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="51adc-177">Non può essere combinato con l'approccio database. UseTransaction () delle sezioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="51adc-177">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="51adc-178">Le eccezioni vengono generate se si inviano istruzioni DDL e non sono state abilitate transazioni distribuite tramite il servizio MSDTC.</span><span class="sxs-lookup"><span data-stu-id="51adc-178">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="51adc-179">Vantaggi dell'approccio TransactionScope:</span><span class="sxs-lookup"><span data-stu-id="51adc-179">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="51adc-180">Verrà automaticamente aggiornata una transazione locale a una transazione distribuita se si effettuano più connessioni a un determinato database o si combina una connessione a un database con una connessione a un database diverso all'interno della stessa transazione. Nota: è necessario che il servizio MSDTC sia configurato per consentire il funzionamento delle transazioni distribuite.</span><span class="sxs-lookup"><span data-stu-id="51adc-180">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="51adc-181">Semplicità di codifica.</span><span class="sxs-lookup"><span data-stu-id="51adc-181">Ease of coding.</span></span> <span data-ttu-id="51adc-182">Se si preferisce che la transazione sia in ambiente e gestita in modo implicito in background, anziché in modo esplicito sotto il controllo, l'approccio TransactionScope può essere più adatto.</span><span class="sxs-lookup"><span data-stu-id="51adc-182">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="51adc-183">In breve, con le nuove API database. BeginTransaction () e database. UseTransaction (), l'approccio TransactionScope non è più necessario per la maggior parte degli utenti.</span><span class="sxs-lookup"><span data-stu-id="51adc-183">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="51adc-184">Se si continua a usare TransactionScope, tenere presente le limitazioni sopra riportate.</span><span class="sxs-lookup"><span data-stu-id="51adc-184">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="51adc-185">È consigliabile usare invece l'approccio descritto nelle sezioni precedenti, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="51adc-185">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
