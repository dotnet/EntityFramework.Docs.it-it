---
title: Resilienza della connessione e logica di ripetizione dei tentativi-EF6
description: Resilienza della connessione e logica di ripetizione dei tentativi in Entity Framework 6
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073188"
---
# <a name="connection-resiliency-and-retry-logic"></a>Resilienza della connessione e logica di ripetizione dei tentativi
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Le applicazioni che si connettono a un server di database sono sempre state vulnerabili alle interruzioni di connessione a causa di errori di back-end e di instabilità della rete Tuttavia, in un ambiente basato su LAN che utilizza server di database dedicati questi errori sono abbastanza rari che la logica aggiuntiva per gestire tali errori non è spesso necessaria. Con l'incremento dei server di database basati su cloud, ad esempio il database SQL di Windows Azure e le connessioni su reti meno affidabili, è ora più comune che si verifichino interruzioni di connessione. Questo problema può essere dovuto a tecniche difensive che i database cloud utilizzano per garantire la correttezza del servizio, ad esempio la limitazione della connessione, o l'instabilità della rete, causando timeout intermittenti e altri errori temporanei.  

La resilienza delle connessioni si riferisce alla possibilità per EF di ritentare automaticamente i comandi che non riescono a causa di questi interruzioni di connessione  

## <a name="execution-strategies"></a>Strategie di esecuzione  

Il tentativo di connessione viene gestito da un'implementazione dell'interfaccia IDbExecutionStrategy. Le implementazioni di IDbExecutionStrategy saranno responsabili dell'accettazione di un'operazione e, se si verifica un'eccezione, che determina se un nuovo tentativo è appropriato e se è in corso un nuovo tentativo. Con EF sono disponibili quattro strategie di esecuzione:  

1. **DefaultExecutionStrategy**: questa strategia di esecuzione non tenta di eseguire alcuna operazione. si tratta dell'impostazione predefinita per i database diversi da SQL Server.  
2. **DefaultSqlExecutionStrategy**: si tratta di una strategia di esecuzione interna utilizzata per impostazione predefinita. Questa strategia non esegue alcun tentativo, tuttavia, eseguirà il wrapping di tutte le eccezioni che potrebbero essere temporanee per informare gli utenti che potrebbero voler abilitare la resilienza della connessione.  
3. **DbExecutionStrategy**: questa classe è adatta come classe base per altre strategie di esecuzione, incluse quelle personalizzate. Implementa un criterio di ripetizione esponenziale, dove il tentativo iniziale si verifica con un ritardo zero e il ritardo aumenta in modo esponenziale fino a quando non viene raggiunto il numero massimo di tentativi. Questa classe dispone di un metodo astratto ShouldRetryOn che può essere implementato in strategie di esecuzione derivate per controllare quali eccezioni devono essere ritentate.  
4. **SqlAzureExecutionStrategy**: questa strategia di esecuzione eredita da DbExecutionStrategy e tenterà di ritentare le eccezioni che potrebbero essere transitorie quando si lavora con il database SQL di Azure.

> [!NOTE]
> Le strategie di esecuzione 2 e 4 sono incluse nel provider SQL Server fornito con EF, che si trova nell'assembly EntityFramework. SqlServer e sono progettate per funzionare con SQL Server.  

## <a name="enabling-an-execution-strategy"></a>Abilitazione di una strategia di esecuzione  

Il modo più semplice per indicare a EF di usare una strategia di esecuzione è con il metodo SetExecutionStrategy della classe [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) :  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

Questo codice indica a EF di usare SqlAzureExecutionStrategy durante la connessione a SQL Server.  

## <a name="configuring-the-execution-strategy"></a>Configurazione della strategia di esecuzione  

Il costruttore di SqlAzureExecutionStrategy può accettare due parametri, MaxRetryCount e MaxDelay. MaxRetry count è il numero massimo di volte in cui la strategia tenterà di riprovare. MaxDelay è un intervallo di tempo che rappresenta il ritardo massimo tra i tentativi che la strategia di esecuzione utilizzerà.  

Per impostare il numero massimo di tentativi su 1 e il ritardo massimo su 30 secondi, eseguire le operazioni seguenti:  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

Il SqlAzureExecutionStrategy tenterà immediatamente la prima volta che si verifica un errore temporaneo, ma ritarderà tra un tentativo e l'altro fino a quando non viene superato il limite massimo di tentativi o il tempo totale raggiunge il ritardo massimo.  

Le strategie di esecuzione ripeteranno solo un numero limitato di eccezioni generalmente temporanee, sarà comunque necessario gestire altri errori, oltre a intercettare l'eccezione RetryLimitExceeded per il caso in cui un errore non è temporaneo o impiega troppo tempo per risolversi.  

Esistono alcune limitazioni quando si usa una strategia di esecuzione di ripetizione dei tentativi:  

## <a name="streaming-queries-are-not-supported"></a>Le query di streaming non sono supportate  

Per impostazione predefinita, EF6 e versioni successive registreranno i risultati della query anziché eseguirne il flusso. Se si desidera che i risultati vengano trasmessi, è possibile utilizzare il metodo AsStreaming per modificare una query LINQ to Entities per la trasmissione.  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

Il flusso non è supportato quando viene registrata una strategia di esecuzione ritentata. Questa limitazione è dovuta al fatto che la connessione può essere trasformata in modo parziale attraverso i risultati restituiti. Quando si verifica questa situazione, EF deve eseguire di nuovo l'intera query, ma non dispone di un modo affidabile per sapere quali risultati sono già stati restituiti (i dati potrebbero essere stati modificati dopo l'invio della query iniziale, i risultati possono essere restituiti in un ordine diverso, i risultati potrebbero non avere un identificatore univoco e così via).  

## <a name="user-initiated-transactions-are-not-supported"></a>Le transazioni avviate dall'utente non sono supportate  

Una volta configurata una strategia di esecuzione che comporta nuovi tentativi, esistono alcune limitazioni relative all'utilizzo delle transazioni.  

Per impostazione predefinita, EF eseguirà tutti gli aggiornamenti del database all'interno di una transazione. Non è necessario eseguire alcuna operazione per abilitare questa operazione, EF esegue sempre questa operazione automaticamente.  

Ad esempio, nel codice SaveChanges seguente viene eseguito automaticamente all'interno di una transazione. Se il metodo SaveChanges ha esito negativo dopo l'inserimento di uno dei nuovi siti, viene eseguito il rollback della transazione e non viene applicata alcuna modifica al database. Il contesto viene inoltre lasciato in uno stato che consente di richiamare SaveChanges per ritentare l'applicazione delle modifiche.  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

Quando non si utilizza una strategia di esecuzione di ripetizione di tentativi, è possibile eseguire il wrapping di più operazioni in una singola transazione. Il codice seguente, ad esempio, esegue il wrapping di due chiamate SaveChanges in un'unica transazione. Se una parte di entrambe le operazioni ha esito negativo, non viene applicata nessuna delle modifiche.  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

Questa operazione non è supportata quando si usa una strategia di esecuzione di ripetizione dei tentativi perché EF non è a conoscenza di alcuna operazione precedente e come riprovare. Se ad esempio il secondo SaveChanges ha avuto esito negativo, EF non dispone più delle informazioni necessarie per ripetere la prima chiamata a SaveChanges.  

### <a name="solution-manually-call-execution-strategy"></a>Soluzione: chiamare manualmente la strategia di esecuzione  

La soluzione consiste nell'usare manualmente la strategia di esecuzione e fornire l'intero set di logica da eseguire, in modo da poter ritentare tutti i tentativi se una delle operazioni ha esito negativo. Quando viene eseguita una strategia di esecuzione derivata da DbExecutionStrategy, la strategia di esecuzione implicita utilizzata in SaveChanges verrà sospesa.  

Si noti che tutti i contesti devono essere costruiti all'interno del blocco di codice per poter essere ripetuti. In questo modo si inizia con uno stato pulito per ogni nuovo tentativo.  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
