---
title: Transazioni - EF Core
description: Gestione delle transazioni per l'atomicità quando si salvano i dati con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/transactions
ms.openlocfilehash: d824db6a4d6e1fc0fc385f007ccfc2c6cbbcae79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070835"
---
# <a name="using-transactions"></a><span data-ttu-id="2d338-103">Utilizzo di transazioni</span><span class="sxs-lookup"><span data-stu-id="2d338-103">Using Transactions</span></span>

<span data-ttu-id="2d338-104">Le transazioni consentono di elaborare varie operazioni di database in modo atomico.</span><span class="sxs-lookup"><span data-stu-id="2d338-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="2d338-105">Se viene eseguito il commit della transazione, tutte le operazioni vengono applicate correttamente nel database.</span><span class="sxs-lookup"><span data-stu-id="2d338-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="2d338-106">Se viene eseguito il rollback della transazione, nessuna delle operazioni viene applicata nel database.</span><span class="sxs-lookup"><span data-stu-id="2d338-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="2d338-107">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="2d338-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="2d338-108">Comportamento delle transazioni predefinito</span><span class="sxs-lookup"><span data-stu-id="2d338-108">Default transaction behavior</span></span>

<span data-ttu-id="2d338-109">Per impostazione predefinita, se il provider di database supporta le transazioni, tutte le modifiche in una singola chiamata a `SaveChanges()` vengono applicate in una transazione.</span><span class="sxs-lookup"><span data-stu-id="2d338-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="2d338-110">Se le modifiche hanno esito negativo, viene eseguito il rollback della transazione e nessuna delle modifiche viene applicata al database.</span><span class="sxs-lookup"><span data-stu-id="2d338-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="2d338-111">Ciò significa che è garantito che l'operazione `SaveChanges()` venga completata correttamente oppure che lasci il database non modificato se si verifica un errore.</span><span class="sxs-lookup"><span data-stu-id="2d338-111">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="2d338-112">Per la maggior parte delle applicazioni, questo comportamento predefinito è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="2d338-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="2d338-113">È consigliabile controllare le transazioni manualmente solo se i requisiti dell'applicazione lo rendono necessario.</span><span class="sxs-lookup"><span data-stu-id="2d338-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="2d338-114">Controllo delle transazioni</span><span class="sxs-lookup"><span data-stu-id="2d338-114">Controlling transactions</span></span>

<span data-ttu-id="2d338-115">È possibile usare l'API `DbContext.Database` per avviare le transazioni, eseguirne il commit ed eseguirne il rollback.</span><span class="sxs-lookup"><span data-stu-id="2d338-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="2d338-116">L'esempio seguente mostra due operazioni `SaveChanges()` e una query LINQ in esecuzione in una singola transazione.</span><span class="sxs-lookup"><span data-stu-id="2d338-116">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="2d338-117">Non tutti i provider di database supportano le transazioni.</span><span class="sxs-lookup"><span data-stu-id="2d338-117">Not all database providers support transactions.</span></span> <span data-ttu-id="2d338-118">Alcuni provider possono generare eccezioni o non eseguire alcuna operazione quando vengono chiamate API per le transazioni.</span><span class="sxs-lookup"><span data-stu-id="2d338-118">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="2d338-119">Transazione tra contesti diversi (solo database relazionali)</span><span class="sxs-lookup"><span data-stu-id="2d338-119">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="2d338-120">È anche possibile condividere una transazione tra più istanze di contesto.</span><span class="sxs-lookup"><span data-stu-id="2d338-120">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="2d338-121">Questa funzionalità è disponibile solo quando si usa un provider di database relazionale, perché richiede l'uso di `DbTransaction` e `DbConnection`, specifici per i database relazionali.</span><span class="sxs-lookup"><span data-stu-id="2d338-121">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="2d338-122">Per condividere una transazione, è necessario che i contesti condividano sia `DbConnection` che `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="2d338-122">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="2d338-123">Consentire connessioni dall'esterno</span><span class="sxs-lookup"><span data-stu-id="2d338-123">Allow connection to be externally provided</span></span>

<span data-ttu-id="2d338-124">La condivisione di `DbConnection` richiede la possibilità di passare una connessione in un contesto durante la costruzione.</span><span class="sxs-lookup"><span data-stu-id="2d338-124">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="2d338-125">Il modo più semplice per consentire `DbConnection` dall'esterno consiste nell'interrompere l'uso del metodo `DbContext.OnConfiguring` per configurare il contesto e nel creare esternamente `DbContextOptions` e passare tale opzioni al costruttore del contesto.</span><span class="sxs-lookup"><span data-stu-id="2d338-125">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="2d338-126">`DbContextOptionsBuilder` è l'API usata in `DbContext.OnConfiguring` per configurare il contesto. In questo caso viene usata esternamente per creare `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="2d338-126">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="2d338-127">In alternativa è possibile continuare a usare `DbContext.OnConfiguring`, accettando però una `DbConnection` che viene salvata e quindi usata in `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="2d338-127">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="2d338-128">Condividere connessione e transazione</span><span class="sxs-lookup"><span data-stu-id="2d338-128">Share connection and transaction</span></span>

<span data-ttu-id="2d338-129">È ora possibile creare più istanze di contesto che condividono la stessa connessione.</span><span class="sxs-lookup"><span data-stu-id="2d338-129">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="2d338-130">Usare quindi l'API `DbContext.Database.UseTransaction(DbTransaction)` per includere entrambi i contesti nella stessa transazione.</span><span class="sxs-lookup"><span data-stu-id="2d338-130">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="2d338-131">Uso di DbTransaction esterne (solo database relazionali)</span><span class="sxs-lookup"><span data-stu-id="2d338-131">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="2d338-132">Se si usano più tecnologie di accesso ai dati per accedere a un database relazionale, può essere utile condividere una transazione tra le operazioni eseguite da queste diverse tecnologie.</span><span class="sxs-lookup"><span data-stu-id="2d338-132">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="2d338-133">L'esempio seguente mostra come eseguire un'operazione ADO.NET SqlClient e un'operazione di Entity Framework Core nella stessa transazione.</span><span class="sxs-lookup"><span data-stu-id="2d338-133">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="2d338-134">Utilizzo di System.Transactions</span><span class="sxs-lookup"><span data-stu-id="2d338-134">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="2d338-135">Questa funzionalità è stata introdotta in EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="2d338-135">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="2d338-136">È possibile usare le transazioni di ambiente, se è necessario coordinarle in un ambito più ampio.</span><span class="sxs-lookup"><span data-stu-id="2d338-136">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="2d338-137">È anche supportato l'inserimento in una transazione esplicita.</span><span class="sxs-lookup"><span data-stu-id="2d338-137">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="2d338-138">Limitazioni di System.Transactions</span><span class="sxs-lookup"><span data-stu-id="2d338-138">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="2d338-139">EF Core si basa sui provider di database per implementare il supporto per System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="2d338-139">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="2d338-140">Anche se il supporto è piuttosto comune tra i provider ADO.NET per .NET Framework, l'API è stata aggiunta solo di recente a .NET Core e il supporto di conseguenza non è ancora molto diffuso.</span><span class="sxs-lookup"><span data-stu-id="2d338-140">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="2d338-141">Se un provider non implementa il supporto per System.Transactions, è possibile che le chiamate a queste API vengano ignorate completamente.</span><span class="sxs-lookup"><span data-stu-id="2d338-141">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="2d338-142">SqlClient per .NET Core offre questo supporto dalla versione 2.1 in poi.</span><span class="sxs-lookup"><span data-stu-id="2d338-142">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="2d338-143">SqlClient per .NET Core 2.0 genererà un'eccezione se si tenta di usare la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="2d338-143">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="2d338-144">È consigliabile verificare che il comportamento dell'API con il provider sia corretto prima di basarsi su di essa per la gestione delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="2d338-144">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="2d338-145">In caso contrario, è consigliabile contattare il gestore del provider del database.</span><span class="sxs-lookup"><span data-stu-id="2d338-145">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="2d338-146">A partire dalla versione 2.1, l'implementazione di System.Transactions in .NET Core non include il supporto per le transazioni distribuite, quindi non è possibile usare `TransactionScope` o `CommittableTransaction` per coordinare le transazioni tra più gestori di risorse.</span><span class="sxs-lookup"><span data-stu-id="2d338-146">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
