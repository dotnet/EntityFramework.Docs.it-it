---
title: Gestione degli errori di commit delle transazioni-EF6
description: Gestione degli errori di commit delle transazioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: 10cb67837264bea37a0621aa078e1753af954c2f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616289"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="a3ff1-103">Gestione degli errori di commit delle transazioni</span><span class="sxs-lookup"><span data-stu-id="a3ff1-103">Handling transaction commit failures</span></span>

> [!NOTE]
> <span data-ttu-id="a3ff1-104">**Ef 6.1 e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-104">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="a3ff1-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a3ff1-106">Come parte di 6,1, viene introdotta una nuova funzionalità di resilienza della connessione per EF: la possibilità di rilevare e ripristinare automaticamente gli errori di connessione temporanei che influiscono sul riconoscimento dei commit delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-106">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="a3ff1-107">I dettagli completi dello scenario sono descritti meglio nel post di Blog [relativo alla connettività del database SQL e al problema idempotenza](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span><span class="sxs-lookup"><span data-stu-id="a3ff1-107">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="a3ff1-108">In breve, lo scenario è che, quando viene generata un'eccezione durante un commit della transazione, esistono due possibili cause:</span><span class="sxs-lookup"><span data-stu-id="a3ff1-108">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="a3ff1-109">Il commit della transazione non è riuscito nel server</span><span class="sxs-lookup"><span data-stu-id="a3ff1-109">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="a3ff1-110">Il commit della transazione è riuscito sul server ma un problema di connettività ha impedito al client di raggiungere il successo della notifica</span><span class="sxs-lookup"><span data-stu-id="a3ff1-110">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="a3ff1-111">Quando si verifica la prima situazione, l'applicazione o l'utente può ritentare l'operazione, ma quando si verificano tentativi è consigliabile evitare il ripristino automatico dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-111">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="a3ff1-112">Il problema è che, senza la possibilità di rilevare il motivo effettivo per cui è stata segnalata un'eccezione durante il commit, l'applicazione non può scegliere la giusta linea di azione.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-112">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="a3ff1-113">La nuova funzionalità di EF 6,1 consente a EF di eseguire un doppio controllo con il database se la transazione ha avuto esito positivo e di intraprendere il giusto corso di azione in modo trasparente.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-113">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="a3ff1-114">Uso della funzionalità</span><span class="sxs-lookup"><span data-stu-id="a3ff1-114">Using the feature</span></span>  

<span data-ttu-id="a3ff1-115">Per abilitare la funzionalità è necessario includere una chiamata a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) nel costruttore di **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-115">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="a3ff1-116">Se non si ha familiarità con **DbConfiguration**, vedere [configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="a3ff1-116">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span> <span data-ttu-id="a3ff1-117">Questa funzionalità può essere utilizzata in combinazione con i tentativi automatici introdotti in EF6, che contribuiscono alla situazione in cui la transazione non è riuscita a eseguire il commit sul server a causa di un errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="a3ff1-117">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="a3ff1-118">Modalità di rilevamento delle transazioni</span><span class="sxs-lookup"><span data-stu-id="a3ff1-118">How transactions are tracked</span></span>  

<span data-ttu-id="a3ff1-119">Quando la funzionalità è abilitata, EF aggiungerà automaticamente una nuova tabella al database denominato **__Transactions**.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-119">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="a3ff1-120">Una nuova riga viene inserita in questa tabella ogni volta che una transazione viene creata da EF e tale riga viene verificata se si verifica un errore di transazione durante il commit.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-120">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="a3ff1-121">Sebbene EF esegua il massimo sforzo per eliminare le righe dalla tabella quando non sono più necessarie, è possibile che la tabella cresca se l'applicazione viene chiusa in modo anomalo e per questo motivo potrebbe essere necessario ripulire la tabella manualmente in alcuni casi.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-121">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="a3ff1-122">Come gestire gli errori di commit con le versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="a3ff1-122">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="a3ff1-123">Prima di EF 6,1 non era disponibile un meccanismo per gestire gli errori di commit nel prodotto EF.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-123">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="a3ff1-124">Esistono diversi modi per gestire questa situazione che possono essere applicati alle versioni precedenti di EF6:</span><span class="sxs-lookup"><span data-stu-id="a3ff1-124">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="a3ff1-125">Opzione 1: non eseguire alcuna operazione</span><span class="sxs-lookup"><span data-stu-id="a3ff1-125">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="a3ff1-126">La probabilità di un errore di connessione durante il commit della transazione è bassa, quindi potrebbe essere accettabile che l'applicazione abbia esito negativo solo se questa condizione si verifica effettivamente.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-126">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="a3ff1-127">Opzione 2: usare il database per reimpostare lo stato</span><span class="sxs-lookup"><span data-stu-id="a3ff1-127">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="a3ff1-128">Ignora il DbContext corrente</span><span class="sxs-lookup"><span data-stu-id="a3ff1-128">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="a3ff1-129">Creare un nuovo DbContext e ripristinare lo stato dell'applicazione dal database</span><span class="sxs-lookup"><span data-stu-id="a3ff1-129">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="a3ff1-130">Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente</span><span class="sxs-lookup"><span data-stu-id="a3ff1-130">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="a3ff1-131">Opzione 3: rilevare manualmente la transazione</span><span class="sxs-lookup"><span data-stu-id="a3ff1-131">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="a3ff1-132">Aggiungere una tabella non rilevata al database utilizzato per tenere traccia dello stato delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-132">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="a3ff1-133">Inserire una riga nella tabella all'inizio di ogni transazione.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-133">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="a3ff1-134">Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-134">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     * <span data-ttu-id="a3ff1-135">Se la riga è presente, continuare normalmente, perché il commit della transazione è stato eseguito correttamente</span><span class="sxs-lookup"><span data-stu-id="a3ff1-135">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     * <span data-ttu-id="a3ff1-136">Se la riga è assente, utilizzare una strategia di esecuzione per ritentare l'operazione corrente.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-136">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="a3ff1-137">Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-137">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="a3ff1-138">[Questo post di Blog](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contiene il codice di esempio per eseguire questa operazione in SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="a3ff1-138">[This blog post](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
