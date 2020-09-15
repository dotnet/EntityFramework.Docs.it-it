---
title: Resilienza della connessione-EF Core
description: Uso della resilienza della connessione per ritentare automaticamente i comandi non riusciti con Entity Framework Core
author: rowanmiller
ms.date: 11/15/2016
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: f4077672a9234bf66dc488d7450f437452e542b6
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071706"
---
# <a name="connection-resiliency"></a><span data-ttu-id="9a802-103">Resilienza della connessione</span><span class="sxs-lookup"><span data-stu-id="9a802-103">Connection Resiliency</span></span>

<span data-ttu-id="9a802-104">La resilienza della connessione ritenta automaticamente i comandi di database non riusciti.</span><span class="sxs-lookup"><span data-stu-id="9a802-104">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="9a802-105">La funzionalità può essere utilizzata con qualsiasi database fornendo una "strategia di esecuzione", che incapsula la logica necessaria per rilevare gli errori e ripetere i comandi.</span><span class="sxs-lookup"><span data-stu-id="9a802-105">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="9a802-106">I provider EF Core possono fornire strategie di esecuzione personalizzate per le specifiche condizioni di errore del database e criteri di ripetizione dei tentativi ottimali.</span><span class="sxs-lookup"><span data-stu-id="9a802-106">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="9a802-107">Ad esempio, il provider di SQL Server include una strategia di esecuzione appositamente adattata per SQL Server (incluso SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="9a802-107">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="9a802-108">È a conoscenza dei tipi di eccezione che è possibile ritentare e ha valori predefiniti ragionevoli per il numero massimo di tentativi, un ritardo tra i tentativi e così via.</span><span class="sxs-lookup"><span data-stu-id="9a802-108">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="9a802-109">Quando si configurano le opzioni per il contesto, viene specificata una strategia di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9a802-109">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="9a802-110">Si tratta in genere del `OnConfiguring` metodo del contesto derivato:</span><span class="sxs-lookup"><span data-stu-id="9a802-110">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="9a802-111">o in `Startup.cs` per un'applicazione ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a802-111">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="9a802-112">Strategia di esecuzione personalizzata</span><span class="sxs-lookup"><span data-stu-id="9a802-112">Custom execution strategy</span></span>

<span data-ttu-id="9a802-113">È disponibile un meccanismo per registrare una strategia di esecuzione personalizzata personalizzata se si desidera modificare le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="9a802-113">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="9a802-114">Strategie di esecuzione e transazioni</span><span class="sxs-lookup"><span data-stu-id="9a802-114">Execution strategies and transactions</span></span>

<span data-ttu-id="9a802-115">Una strategia di esecuzione che esegue automaticamente nuovi tentativi in caso di errori deve essere in grado di riprodurre ogni operazione in un blocco di tentativi che ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9a802-115">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="9a802-116">Quando sono abilitati nuovi tentativi, ogni operazione eseguita tramite EF Core diventa la propria operazione riproducibile.</span><span class="sxs-lookup"><span data-stu-id="9a802-116">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="9a802-117">Ovvero ogni query e ogni chiamata a `SaveChanges()` verrà ritentata come un'unità se si verifica un errore temporaneo.</span><span class="sxs-lookup"><span data-stu-id="9a802-117">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="9a802-118">Tuttavia, se il codice avvia una transazione utilizzando `BeginTransaction()` , si definisce un gruppo di operazioni che deve essere considerato come un'unità e tutti gli elementi all'interno della transazione dovranno essere riprodotti in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="9a802-118">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="9a802-119">Se si tenta di eseguire questa operazione quando si usa una strategia di esecuzione, verrà generata un'eccezione simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="9a802-119">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="9a802-120">InvalidOperationException: la strategia di esecuzione configurata ' SqlServerRetryingExecutionStrategy ' non supporta le transazioni avviate dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9a802-120">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="9a802-121">Usare la strategia di esecuzione restituita da 'DbContext.Database.CreateExecutionStrategy()' per eseguire tutte le operazioni nella transazione come un'unità con possibilità di ritentare.</span><span class="sxs-lookup"><span data-stu-id="9a802-121">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="9a802-122">La soluzione consiste nel richiamare manualmente la strategia di esecuzione con un delegato che rappresenta tutti gli elementi che devono essere eseguiti.</span><span class="sxs-lookup"><span data-stu-id="9a802-122">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="9a802-123">Se si verifica un errore temporaneo, la strategia di esecuzione chiamerà nuovamente il delegato.</span><span class="sxs-lookup"><span data-stu-id="9a802-123">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="9a802-124">Questo approccio può essere utilizzato anche con le transazioni di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9a802-124">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="9a802-125">Errore di commit delle transazioni e problema idempotenza</span><span class="sxs-lookup"><span data-stu-id="9a802-125">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="9a802-126">In generale, quando si verifica un errore di connessione, viene eseguito il rollback della transazione corrente.</span><span class="sxs-lookup"><span data-stu-id="9a802-126">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="9a802-127">Tuttavia, se la connessione viene eliminata mentre viene eseguito il commit della transazione, lo stato risultante della transazione è sconosciuto.</span><span class="sxs-lookup"><span data-stu-id="9a802-127">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="9a802-128">Per impostazione predefinita, la strategia di esecuzione tenterà di ritentare l'operazione come se fosse stato eseguito il rollback della transazione. in caso contrario, si verificherà un'eccezione se il nuovo stato del database è incompatibile o potrebbe causare un **danneggiamento dei dati** se l'operazione non si basa su un determinato stato, ad esempio quando si inserisce una nuova riga con valori di chiave generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9a802-128">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="9a802-129">Esistono diversi modi per gestire questo problema.</span><span class="sxs-lookup"><span data-stu-id="9a802-129">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="9a802-130">Opzione 1-do (quasi) niente</span><span class="sxs-lookup"><span data-stu-id="9a802-130">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="9a802-131">La probabilità di un errore di connessione durante il commit della transazione è bassa, quindi potrebbe essere accettabile che l'applicazione abbia esito negativo solo se questa condizione si verifica effettivamente.</span><span class="sxs-lookup"><span data-stu-id="9a802-131">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="9a802-132">Tuttavia, è necessario evitare di utilizzare chiavi generate dall'archivio per assicurarsi che venga generata un'eccezione anziché aggiungere una riga duplicata.</span><span class="sxs-lookup"><span data-stu-id="9a802-132">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="9a802-133">Si consiglia di utilizzare un valore GUID generato dal client o un generatore di valori sul lato client.</span><span class="sxs-lookup"><span data-stu-id="9a802-133">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="9a802-134">Opzione 2: ricompilare lo stato dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="9a802-134">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="9a802-135">Elimina l'oggetto corrente `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="9a802-135">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="9a802-136">Creare un nuovo `DbContext` e ripristinare lo stato dell'applicazione dal database.</span><span class="sxs-lookup"><span data-stu-id="9a802-136">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="9a802-137">Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="9a802-137">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="9a802-138">Opzione 3: aggiungere la verifica dello stato</span><span class="sxs-lookup"><span data-stu-id="9a802-138">Option 3 - Add state verification</span></span>

<span data-ttu-id="9a802-139">Per la maggior parte delle operazioni che modificano lo stato del database, è possibile aggiungere il codice che controlla se è riuscito.</span><span class="sxs-lookup"><span data-stu-id="9a802-139">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="9a802-140">EF fornisce un metodo di estensione per semplificare questa operazione `IExecutionStrategy.ExecuteInTransaction` .</span><span class="sxs-lookup"><span data-stu-id="9a802-140">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="9a802-141">Questo metodo avvia ed esegue il commit di una transazione e accetta anche una funzione nel `verifySucceeded` parametro richiamato quando si verifica un errore temporaneo durante il commit della transazione.</span><span class="sxs-lookup"><span data-stu-id="9a802-141">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="9a802-142">Qui `SaveChanges` viene richiamato con `acceptAllChangesOnSuccess` impostato su `false` per evitare di modificare lo stato dell' `Blog` entità in se ha `Unchanged` `SaveChanges` esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9a802-142">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="9a802-143">In questo modo è possibile ripetere la stessa operazione se il commit ha esito negativo e viene eseguito il rollback della transazione.</span><span class="sxs-lookup"><span data-stu-id="9a802-143">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="9a802-144">Opzione 4: rilevare manualmente la transazione</span><span class="sxs-lookup"><span data-stu-id="9a802-144">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="9a802-145">Se è necessario usare chiavi generate dall'archivio o è necessario un metodo generico per gestire gli errori di commit che non dipendono dall'operazione eseguita, a ogni transazione potrebbe essere assegnato un ID verificato quando il commit non riesce.</span><span class="sxs-lookup"><span data-stu-id="9a802-145">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="9a802-146">Aggiungere una tabella al database utilizzato per tenere traccia dello stato delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="9a802-146">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="9a802-147">Inserire una riga nella tabella all'inizio di ogni transazione.</span><span class="sxs-lookup"><span data-stu-id="9a802-147">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="9a802-148">Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.</span><span class="sxs-lookup"><span data-stu-id="9a802-148">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="9a802-149">Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.</span><span class="sxs-lookup"><span data-stu-id="9a802-149">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="9a802-150">Verificare che il contesto utilizzato per la verifica disponga di una strategia di esecuzione definita poiché è probabile che la connessione non riesca di nuovo durante la verifica in caso di errore durante il commit della transazione.</span><span class="sxs-lookup"><span data-stu-id="9a802-150">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
