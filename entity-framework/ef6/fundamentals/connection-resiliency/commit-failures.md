---
title: Gestione degli errori di commit delle transazioni-EF6
description: Gestione degli errori di commit delle transazioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: c817e5e9342d5e0682318dc34fa3b4cdfa756228
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072850"
---
# <a name="handling-transaction-commit-failures"></a>Gestione degli errori di commit delle transazioni

> [!NOTE]
> **Ef 6.1 e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 6,1. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Come parte di 6,1, viene introdotta una nuova funzionalità di resilienza della connessione per EF: la possibilità di rilevare e ripristinare automaticamente gli errori di connessione temporanei che influiscono sul riconoscimento dei commit delle transazioni. I dettagli completi dello scenario sono descritti meglio nel post di Blog [relativo alla connettività del database SQL e al problema idempotenza](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).  In breve, lo scenario è che, quando viene generata un'eccezione durante un commit della transazione, esistono due possibili cause:  

1. Il commit della transazione non è riuscito nel server
2. Il commit della transazione è riuscito sul server ma un problema di connettività ha impedito al client di raggiungere il successo della notifica  

Quando si verifica la prima situazione, l'applicazione o l'utente può ritentare l'operazione, ma quando si verificano tentativi è consigliabile evitare il ripristino automatico dell'applicazione. Il problema è che, senza la possibilità di rilevare il motivo effettivo per cui è stata segnalata un'eccezione durante il commit, l'applicazione non può scegliere la giusta linea di azione. La nuova funzionalità di EF 6,1 consente a EF di eseguire un doppio controllo con il database se la transazione ha avuto esito positivo e di intraprendere il giusto corso di azione in modo trasparente.  

## <a name="using-the-feature"></a>Uso della funzionalità  

Per abilitare la funzionalità è necessario includere una chiamata a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) nel costruttore di **DbConfiguration**. Se non si ha familiarità con **DbConfiguration**, vedere [configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based). Questa funzionalità può essere utilizzata in combinazione con i tentativi automatici introdotti in EF6, che contribuiscono alla situazione in cui la transazione non è riuscita a eseguire il commit sul server a causa di un errore temporaneo:  

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

## <a name="how-transactions-are-tracked"></a>Modalità di rilevamento delle transazioni  

Quando la funzionalità è abilitata, EF aggiungerà automaticamente una nuova tabella al database denominato **__Transactions**. Una nuova riga viene inserita in questa tabella ogni volta che una transazione viene creata da EF e tale riga viene verificata se si verifica un errore di transazione durante il commit.  

Sebbene EF esegua il massimo sforzo per eliminare le righe dalla tabella quando non sono più necessarie, è possibile che la tabella cresca se l'applicazione viene chiusa in modo anomalo e per questo motivo potrebbe essere necessario ripulire la tabella manualmente in alcuni casi.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Come gestire gli errori di commit con le versioni precedenti

Prima di EF 6,1 non era disponibile un meccanismo per gestire gli errori di commit nel prodotto EF. Esistono diversi modi per gestire questa situazione che possono essere applicati alle versioni precedenti di EF6:  

* Opzione 1: non eseguire alcuna operazione  

  La probabilità di un errore di connessione durante il commit della transazione è bassa, quindi potrebbe essere accettabile che l'applicazione abbia esito negativo solo se questa condizione si verifica effettivamente.  

* Opzione 2: usare il database per reimpostare lo stato  

  1. Ignora il DbContext corrente  
  2. Creare un nuovo DbContext e ripristinare lo stato dell'applicazione dal database  
  3. Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente  

* Opzione 3: rilevare manualmente la transazione  

  1. Aggiungere una tabella non rilevata al database utilizzato per tenere traccia dello stato delle transazioni.  
  2. Inserire una riga nella tabella all'inizio di ogni transazione.  
  3. Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.  
     * Se la riga è presente, continuare normalmente, perché il commit della transazione è stato eseguito correttamente  
     * Se la riga è assente, utilizzare una strategia di esecuzione per ritentare l'operazione corrente.  
  4. Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.  

[Questo post di Blog](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contiene il codice di esempio per eseguire questa operazione in SQL Azure.  
