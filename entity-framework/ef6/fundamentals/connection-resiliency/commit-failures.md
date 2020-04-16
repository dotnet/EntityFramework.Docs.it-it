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
# <a name="handling-transaction-commit-failures"></a>Gestione degli errori di commit delle transazioniHandling transaction commit failures
> [!NOTE]
> **EF6.1 Solo onwards:** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 6.1. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Come parte di 6.1 stiamo introducendo una nuova funzionalità di resilienza della connessione per EF: la possibilità di rilevare e recuperare automaticamente quando gli errori di connessione temporanei influiscono sul riconoscimento dei commit delle transazioni. I dettagli completi dello scenario sono meglio descritti nel post di blog [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).  In sintesi, lo scenario è che quando viene generata un'eccezione durante il commit di una transazione ci sono due possibili cause:  

1. Il commit della transazione non è riuscito nel server
2. Il commit della transazione ha avuto esito positivo sul server, ma un problema di connettività ha impedito la notifica di esito positivo per raggiungere il client  

Quando si verifica la prima situazione, l'applicazione o l'utente può ritentare l'operazione, ma quando si verifica la seconda situazione i tentativi devono essere evitati e l'applicazione potrebbe essere ripristinata automaticamente. La sfida è che senza la possibilità di rilevare quale fosse il motivo effettivo è stata segnalata un'eccezione durante il commit, l'applicazione non può scegliere la giusta linea di azione. La nuova funzionalità di EF 6.1 consente a EF di eseguire un doppio controllo con il database se la transazione ha avuto esito positivo e prendere la giusta linea di azione in modo trasparente.  

## <a name="using-the-feature"></a>Uso della funzionalità  

Per abilitare la funzionalità è necessaria includere una chiamata a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) nel costruttore di **DbConfiguration**. Se non si ha familiarità con **DbConfiguration**, vedere [Configurazione basata su codice](~/ef6/fundamentals/configuring/code-based.md). Questa funzionalità può essere utilizzata in combinazione con i tentativi automatici introdotti in EF6, che consentono nella situazione in cui la transazione effettivamente non è riuscito a eseguire il commit sul server a causa di un errore temporaneo:This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:  

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

## <a name="how-transactions-are-tracked"></a>Modalità di registrazione delle transazioni  

Quando la funzionalità è abilitata, EF aggiungerà automaticamente una nuova tabella al database denominato **__Transactions**. Una nuova riga viene inserita in questa tabella ogni volta che viene creata una transazione da EF e tale riga viene verificata l'esistenza se si verifica un errore di transazione durante il commit.  

Anche se EF eseguirà uno sforzo migliore per eliminare le righe dalla tabella quando non sono più necessari, la tabella può aumentare se l'applicazione viene chiusa prematuramente e per questo motivo potrebbe essere necessario eliminare manualmente la tabella in alcuni casi.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Come gestire gli errori di commit con le versioni precedentiHow to handle commit failures with previous Versions

Prima di EF 6.1 non esisteva un meccanismo per gestire gli errori di commit nel prodotto EF. Esistono diversi modi per gestire questa situazione che può essere applicata alle versioni precedenti di EF6:  

* Opzione 1 - Non eseguire alcuna operazioneOption 1 - Do nothing  

  La probabilità di un errore di connessione durante il commit della transazione è bassa, pertanto può essere accettabile che l'applicazione abbia esito negativo solo se si verifica effettivamente questa condizione.  

* Opzione 2 - Utilizzare il database per reimpostare lo statoOption 2 - Use the database to reset state  

  1. Eliminare l'oggetto DbContext corrente  
  2. Creare un nuovo oggetto DbContext e ripristinare lo stato dell'applicazione dal databaseCreate a new DbContext and restore the state of your application from the database  
  3. Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente  

* Opzione 3 - Tenere traccia manualmente della transazioneOption 3 - Manually track the transaction  

  1. Aggiungere una tabella non rilevata al database utilizzato per tenere traccia dello stato delle transazioni.  
  2. Inserire una riga nella tabella all'inizio di ogni transazione.  
  3. Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.  
     - Se la riga è presente, continuare normalmente, poiché è stato eseguito il commit della transazione  
     - Se la riga è assente, utilizzare una strategia di esecuzione per ripetere l'operazione corrente.  
  4. Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.  

[Questo post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) di blog contiene codice di esempio per l'operazione in SQL Azure.This blog post contains sample code for accomplishing this on SQL Azure.  
