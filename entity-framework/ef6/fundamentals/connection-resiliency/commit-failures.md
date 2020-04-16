---
title: Gestione degli errori di commit delle transazioni - EF6Handling transaction commit failures - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434136"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="e7501-102">Gestione degli errori di commit delle transazioniHandling transaction commit failures</span><span class="sxs-lookup"><span data-stu-id="e7501-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="e7501-103">**EF6.1 Solo onwards:** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 6.1.</span><span class="sxs-lookup"><span data-stu-id="e7501-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="e7501-104">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="e7501-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e7501-105">Come parte di 6.1 stiamo introducendo una nuova funzionalità di resilienza della connessione per EF: la possibilità di rilevare e recuperare automaticamente quando gli errori di connessione temporanei influiscono sul riconoscimento dei commit delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="e7501-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="e7501-106">I dettagli completi dello scenario sono meglio descritti nel post di blog [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span><span class="sxs-lookup"><span data-stu-id="e7501-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="e7501-107">In sintesi, lo scenario è che quando viene generata un'eccezione durante il commit di una transazione ci sono due possibili cause:</span><span class="sxs-lookup"><span data-stu-id="e7501-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="e7501-108">Il commit della transazione non è riuscito nel server</span><span class="sxs-lookup"><span data-stu-id="e7501-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="e7501-109">Il commit della transazione ha avuto esito positivo sul server, ma un problema di connettività ha impedito la notifica di esito positivo per raggiungere il client</span><span class="sxs-lookup"><span data-stu-id="e7501-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="e7501-110">Quando si verifica la prima situazione, l'applicazione o l'utente può ritentare l'operazione, ma quando si verifica la seconda situazione i tentativi devono essere evitati e l'applicazione potrebbe essere ripristinata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e7501-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="e7501-111">La sfida è che senza la possibilità di rilevare quale fosse il motivo effettivo è stata segnalata un'eccezione durante il commit, l'applicazione non può scegliere la giusta linea di azione.</span><span class="sxs-lookup"><span data-stu-id="e7501-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="e7501-112">La nuova funzionalità di EF 6.1 consente a EF di eseguire un doppio controllo con il database se la transazione ha avuto esito positivo e prendere la giusta linea di azione in modo trasparente.</span><span class="sxs-lookup"><span data-stu-id="e7501-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="e7501-113">Uso della funzionalità</span><span class="sxs-lookup"><span data-stu-id="e7501-113">Using the feature</span></span>  

<span data-ttu-id="e7501-114">Per abilitare la funzionalità è necessaria includere una chiamata a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) nel costruttore di **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="e7501-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="e7501-115">Se non si ha familiarità con **DbConfiguration**, vedere [Configurazione basata su codice](~/ef6/fundamentals/configuring/code-based.md).</span><span class="sxs-lookup"><span data-stu-id="e7501-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="e7501-116">Questa funzionalità può essere utilizzata in combinazione con i tentativi automatici introdotti in EF6, che consentono nella situazione in cui la transazione effettivamente non è riuscito a eseguire il commit sul server a causa di un errore temporaneo:This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span><span class="sxs-lookup"><span data-stu-id="e7501-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="e7501-117">Modalità di registrazione delle transazioni</span><span class="sxs-lookup"><span data-stu-id="e7501-117">How transactions are tracked</span></span>  

<span data-ttu-id="e7501-118">Quando la funzionalità è abilitata, EF aggiungerà automaticamente una nuova tabella al database denominato **__Transactions**.</span><span class="sxs-lookup"><span data-stu-id="e7501-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="e7501-119">Una nuova riga viene inserita in questa tabella ogni volta che viene creata una transazione da EF e tale riga viene verificata l'esistenza se si verifica un errore di transazione durante il commit.</span><span class="sxs-lookup"><span data-stu-id="e7501-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="e7501-120">Anche se EF eseguirà uno sforzo migliore per eliminare le righe dalla tabella quando non sono più necessari, la tabella può aumentare se l'applicazione viene chiusa prematuramente e per questo motivo potrebbe essere necessario eliminare manualmente la tabella in alcuni casi.</span><span class="sxs-lookup"><span data-stu-id="e7501-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="e7501-121">Come gestire gli errori di commit con le versioni precedentiHow to handle commit failures with previous Versions</span><span class="sxs-lookup"><span data-stu-id="e7501-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="e7501-122">Prima di EF 6.1 non esisteva un meccanismo per gestire gli errori di commit nel prodotto EF.</span><span class="sxs-lookup"><span data-stu-id="e7501-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="e7501-123">Esistono diversi modi per gestire questa situazione che può essere applicata alle versioni precedenti di EF6:</span><span class="sxs-lookup"><span data-stu-id="e7501-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="e7501-124">Opzione 1 - Non eseguire alcuna operazioneOption 1 - Do nothing</span><span class="sxs-lookup"><span data-stu-id="e7501-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="e7501-125">La probabilità di un errore di connessione durante il commit della transazione è bassa, pertanto può essere accettabile che l'applicazione abbia esito negativo solo se si verifica effettivamente questa condizione.</span><span class="sxs-lookup"><span data-stu-id="e7501-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="e7501-126">Opzione 2 - Utilizzare il database per reimpostare lo statoOption 2 - Use the database to reset state</span><span class="sxs-lookup"><span data-stu-id="e7501-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="e7501-127">Eliminare l'oggetto DbContext corrente</span><span class="sxs-lookup"><span data-stu-id="e7501-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="e7501-128">Creare un nuovo oggetto DbContext e ripristinare lo stato dell'applicazione dal databaseCreate a new DbContext and restore the state of your application from the database</span><span class="sxs-lookup"><span data-stu-id="e7501-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="e7501-129">Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente</span><span class="sxs-lookup"><span data-stu-id="e7501-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="e7501-130">Opzione 3 - Tenere traccia manualmente della transazioneOption 3 - Manually track the transaction</span><span class="sxs-lookup"><span data-stu-id="e7501-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="e7501-131">Aggiungere una tabella non rilevata al database utilizzato per tenere traccia dello stato delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="e7501-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="e7501-132">Inserire una riga nella tabella all'inizio di ogni transazione.</span><span class="sxs-lookup"><span data-stu-id="e7501-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="e7501-133">Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.</span><span class="sxs-lookup"><span data-stu-id="e7501-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="e7501-134">Se la riga è presente, continuare normalmente, poiché è stato eseguito il commit della transazione</span><span class="sxs-lookup"><span data-stu-id="e7501-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="e7501-135">Se la riga è assente, utilizzare una strategia di esecuzione per ripetere l'operazione corrente.</span><span class="sxs-lookup"><span data-stu-id="e7501-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="e7501-136">Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.</span><span class="sxs-lookup"><span data-stu-id="e7501-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="e7501-137">[Questo post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) di blog contiene codice di esempio per l'operazione in SQL Azure.This blog post contains sample code for accomplishing this on SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="e7501-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
