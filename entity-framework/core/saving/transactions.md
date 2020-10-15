---
title: Transazioni - EF Core
description: Gestione delle transazioni per l'atomicità quando si salvano i dati con Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: 2cefe23068a40122b7a37c21536213456eef7b66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063621"
---
# <a name="using-transactions"></a><span data-ttu-id="4b17f-103">Utilizzo di transazioni</span><span class="sxs-lookup"><span data-stu-id="4b17f-103">Using Transactions</span></span>

<span data-ttu-id="4b17f-104">Le transazioni consentono di elaborare varie operazioni di database in modo atomico.</span><span class="sxs-lookup"><span data-stu-id="4b17f-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="4b17f-105">Se viene eseguito il commit della transazione, tutte le operazioni vengono applicate correttamente nel database.</span><span class="sxs-lookup"><span data-stu-id="4b17f-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="4b17f-106">Se viene eseguito il rollback della transazione, nessuna delle operazioni viene applicata nel database.</span><span class="sxs-lookup"><span data-stu-id="4b17f-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="4b17f-107">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="4b17f-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="4b17f-108">Comportamento delle transazioni predefinito</span><span class="sxs-lookup"><span data-stu-id="4b17f-108">Default transaction behavior</span></span>

<span data-ttu-id="4b17f-109">Per impostazione predefinita, se il provider di database supporta le transazioni, tutte le modifiche in una singola chiamata a `SaveChanges` vengono applicate in una transazione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="4b17f-110">Se le modifiche hanno esito negativo, viene eseguito il rollback della transazione e nessuna delle modifiche viene applicata al database.</span><span class="sxs-lookup"><span data-stu-id="4b17f-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="4b17f-111">Ciò significa che è garantito che l'operazione `SaveChanges` venga completata correttamente oppure che lasci il database non modificato se si verifica un errore.</span><span class="sxs-lookup"><span data-stu-id="4b17f-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="4b17f-112">Per la maggior parte delle applicazioni, questo comportamento predefinito è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="4b17f-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="4b17f-113">È consigliabile controllare le transazioni manualmente solo se i requisiti dell'applicazione lo rendono necessario.</span><span class="sxs-lookup"><span data-stu-id="4b17f-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="4b17f-114">Controllo delle transazioni</span><span class="sxs-lookup"><span data-stu-id="4b17f-114">Controlling transactions</span></span>

<span data-ttu-id="4b17f-115">È possibile usare l'API `DbContext.Database` per avviare le transazioni, eseguirne il commit ed eseguirne il rollback.</span><span class="sxs-lookup"><span data-stu-id="4b17f-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="4b17f-116">Nell'esempio seguente vengono illustrate due `SaveChanges` operazioni e una query LINQ eseguita in un'unica transazione:</span><span class="sxs-lookup"><span data-stu-id="4b17f-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="4b17f-117">Mentre tutti i provider di database relazionali supportano le transazioni, altri tipi di provider possono generare o no-op quando vengono chiamate le API di transazione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="4b17f-118">Salvataggio</span><span class="sxs-lookup"><span data-stu-id="4b17f-118">Savepoints</span></span>

<span data-ttu-id="4b17f-119">Quando `SaveChanges` viene richiamato ed è già in corso una transazione nel contesto, EF crea automaticamente un *salvataggio* prima di salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="4b17f-119">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="4b17f-120">Salvataggio sono punti all'interno di una transazione di database di cui è possibile eseguire il rollback in un secondo momento, se si verifica un errore o per qualsiasi altro motivo.</span><span class="sxs-lookup"><span data-stu-id="4b17f-120">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="4b17f-121">Se `SaveChanges` si verifica un errore, viene automaticamente eseguito il rollback della transazione a salvataggio, lasciando la transazione nello stesso stato di se non è mai stata avviata.</span><span class="sxs-lookup"><span data-stu-id="4b17f-121">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="4b17f-122">Ciò consente di risolvere eventuali problemi e di ritentare il salvataggio, in particolare quando si verificano problemi di [concorrenza ottimistica](xref:core/saving/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="4b17f-122">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

<span data-ttu-id="4b17f-123">È anche possibile gestire manualmente salvataggio, così come avviene con le transazioni.</span><span class="sxs-lookup"><span data-stu-id="4b17f-123">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="4b17f-124">Nell'esempio seguente viene creato un salvataggio all'interno di una transazione e viene eseguito il rollback in caso di errore:</span><span class="sxs-lookup"><span data-stu-id="4b17f-124">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="4b17f-125">Transazione tra più contesti</span><span class="sxs-lookup"><span data-stu-id="4b17f-125">Cross-context transaction</span></span>

<span data-ttu-id="4b17f-126">È anche possibile condividere una transazione tra più istanze di contesto.</span><span class="sxs-lookup"><span data-stu-id="4b17f-126">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="4b17f-127">Questa funzionalità è disponibile solo quando si usa un provider di database relazionale, perché richiede l'uso di `DbTransaction` e `DbConnection`, specifici per i database relazionali.</span><span class="sxs-lookup"><span data-stu-id="4b17f-127">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="4b17f-128">Per condividere una transazione, è necessario che i contesti condividano sia `DbConnection` che `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="4b17f-128">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="4b17f-129">Consentire connessioni dall'esterno</span><span class="sxs-lookup"><span data-stu-id="4b17f-129">Allow connection to be externally provided</span></span>

<span data-ttu-id="4b17f-130">La condivisione di `DbConnection` richiede la possibilità di passare una connessione in un contesto durante la costruzione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-130">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="4b17f-131">Il modo più semplice per consentire `DbConnection` dall'esterno consiste nell'interrompere l'uso del metodo `DbContext.OnConfiguring` per configurare il contesto e nel creare esternamente `DbContextOptions` e passare tale opzioni al costruttore del contesto.</span><span class="sxs-lookup"><span data-stu-id="4b17f-131">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="4b17f-132">`DbContextOptionsBuilder` è l'API usata in `DbContext.OnConfiguring` per configurare il contesto. In questo caso viene usata esternamente per creare `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="4b17f-132">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="4b17f-133">In alternativa è possibile continuare a usare `DbContext.OnConfiguring`, accettando però una `DbConnection` che viene salvata e quindi usata in `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="4b17f-133">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="4b17f-134">Condividere connessione e transazione</span><span class="sxs-lookup"><span data-stu-id="4b17f-134">Share connection and transaction</span></span>

<span data-ttu-id="4b17f-135">È ora possibile creare più istanze di contesto che condividono la stessa connessione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-135">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="4b17f-136">Usare quindi l'API `DbContext.Database.UseTransaction(DbTransaction)` per includere entrambi i contesti nella stessa transazione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-136">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="4b17f-137">Uso di DbTransaction esterne (solo database relazionali)</span><span class="sxs-lookup"><span data-stu-id="4b17f-137">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="4b17f-138">Se si usano più tecnologie di accesso ai dati per accedere a un database relazionale, può essere utile condividere una transazione tra le operazioni eseguite da queste diverse tecnologie.</span><span class="sxs-lookup"><span data-stu-id="4b17f-138">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="4b17f-139">L'esempio seguente mostra come eseguire un'operazione ADO.NET SqlClient e un'operazione di Entity Framework Core nella stessa transazione.</span><span class="sxs-lookup"><span data-stu-id="4b17f-139">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="4b17f-140">Utilizzo di System.Transactions</span><span class="sxs-lookup"><span data-stu-id="4b17f-140">Using System.Transactions</span></span>

<span data-ttu-id="4b17f-141">È possibile usare le transazioni di ambiente, se è necessario coordinarle in un ambito più ampio.</span><span class="sxs-lookup"><span data-stu-id="4b17f-141">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="4b17f-142">È anche supportato l'inserimento in una transazione esplicita.</span><span class="sxs-lookup"><span data-stu-id="4b17f-142">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="4b17f-143">Limitazioni di System.Transactions</span><span class="sxs-lookup"><span data-stu-id="4b17f-143">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="4b17f-144">EF Core si basa sui provider di database per implementare il supporto per System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="4b17f-144">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="4b17f-145">Se un provider non implementa il supporto per System.Transactions, è possibile che le chiamate a queste API vengano ignorate completamente.</span><span class="sxs-lookup"><span data-stu-id="4b17f-145">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="4b17f-146">SqlClient lo supporta.</span><span class="sxs-lookup"><span data-stu-id="4b17f-146">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="4b17f-147">È consigliabile verificare che il comportamento dell'API con il provider sia corretto prima di basarsi su di essa per la gestione delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="4b17f-147">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="4b17f-148">In caso contrario, è consigliabile contattare il gestore del provider del database.</span><span class="sxs-lookup"><span data-stu-id="4b17f-148">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="4b17f-149">A partire da .NET Core 2,1, l'implementazione System. Transactions non include il supporto per le transazioni distribuite, pertanto non è possibile usare `TransactionScope` o `CommittableTransaction` per coordinare le transazioni tra più gestori di risorse.</span><span class="sxs-lookup"><span data-stu-id="4b17f-149">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
