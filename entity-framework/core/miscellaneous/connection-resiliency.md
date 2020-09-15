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
# <a name="connection-resiliency"></a>Resilienza della connessione

La resilienza della connessione ritenta automaticamente i comandi di database non riusciti. La funzionalità può essere utilizzata con qualsiasi database fornendo una "strategia di esecuzione", che incapsula la logica necessaria per rilevare gli errori e ripetere i comandi. I provider EF Core possono fornire strategie di esecuzione personalizzate per le specifiche condizioni di errore del database e criteri di ripetizione dei tentativi ottimali.

Ad esempio, il provider di SQL Server include una strategia di esecuzione appositamente adattata per SQL Server (incluso SQL Azure). È a conoscenza dei tipi di eccezione che è possibile ritentare e ha valori predefiniti ragionevoli per il numero massimo di tentativi, un ritardo tra i tentativi e così via.

Quando si configurano le opzioni per il contesto, viene specificata una strategia di esecuzione. Si tratta in genere del `OnConfiguring` metodo del contesto derivato:

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

o in `Startup.cs` per un'applicazione ASP.NET Core:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>Strategia di esecuzione personalizzata

È disponibile un meccanismo per registrare una strategia di esecuzione personalizzata personalizzata se si desidera modificare le impostazioni predefinite.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>Strategie di esecuzione e transazioni

Una strategia di esecuzione che esegue automaticamente nuovi tentativi in caso di errori deve essere in grado di riprodurre ogni operazione in un blocco di tentativi che ha esito negativo. Quando sono abilitati nuovi tentativi, ogni operazione eseguita tramite EF Core diventa la propria operazione riproducibile. Ovvero ogni query e ogni chiamata a `SaveChanges()` verrà ritentata come un'unità se si verifica un errore temporaneo.

Tuttavia, se il codice avvia una transazione utilizzando `BeginTransaction()` , si definisce un gruppo di operazioni che deve essere considerato come un'unità e tutti gli elementi all'interno della transazione dovranno essere riprodotti in caso di errore. Se si tenta di eseguire questa operazione quando si usa una strategia di esecuzione, verrà generata un'eccezione simile alla seguente:

> InvalidOperationException: la strategia di esecuzione configurata ' SqlServerRetryingExecutionStrategy ' non supporta le transazioni avviate dall'utente. Usare la strategia di esecuzione restituita da 'DbContext.Database.CreateExecutionStrategy()' per eseguire tutte le operazioni nella transazione come un'unità con possibilità di ritentare.

La soluzione consiste nel richiamare manualmente la strategia di esecuzione con un delegato che rappresenta tutti gli elementi che devono essere eseguiti. Se si verifica un errore temporaneo, la strategia di esecuzione chiamerà nuovamente il delegato.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

Questo approccio può essere utilizzato anche con le transazioni di ambiente.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>Errore di commit delle transazioni e problema idempotenza

In generale, quando si verifica un errore di connessione, viene eseguito il rollback della transazione corrente. Tuttavia, se la connessione viene eliminata mentre viene eseguito il commit della transazione, lo stato risultante della transazione è sconosciuto. 

Per impostazione predefinita, la strategia di esecuzione tenterà di ritentare l'operazione come se fosse stato eseguito il rollback della transazione. in caso contrario, si verificherà un'eccezione se il nuovo stato del database è incompatibile o potrebbe causare un **danneggiamento dei dati** se l'operazione non si basa su un determinato stato, ad esempio quando si inserisce una nuova riga con valori di chiave generati automaticamente.

Esistono diversi modi per gestire questo problema.

### <a name="option-1---do-almost-nothing"></a>Opzione 1-do (quasi) niente

La probabilità di un errore di connessione durante il commit della transazione è bassa, quindi potrebbe essere accettabile che l'applicazione abbia esito negativo solo se questa condizione si verifica effettivamente.

Tuttavia, è necessario evitare di utilizzare chiavi generate dall'archivio per assicurarsi che venga generata un'eccezione anziché aggiungere una riga duplicata. Si consiglia di utilizzare un valore GUID generato dal client o un generatore di valori sul lato client.

### <a name="option-2---rebuild-application-state"></a>Opzione 2: ricompilare lo stato dell'applicazione

1. Elimina l'oggetto corrente `DbContext` .
2. Creare un nuovo `DbContext` e ripristinare lo stato dell'applicazione dal database.
3. Informare l'utente che l'ultima operazione potrebbe non essere stata completata correttamente.

### <a name="option-3---add-state-verification"></a>Opzione 3: aggiungere la verifica dello stato

Per la maggior parte delle operazioni che modificano lo stato del database, è possibile aggiungere il codice che controlla se è riuscito. EF fornisce un metodo di estensione per semplificare questa operazione `IExecutionStrategy.ExecuteInTransaction` .

Questo metodo avvia ed esegue il commit di una transazione e accetta anche una funzione nel `verifySucceeded` parametro richiamato quando si verifica un errore temporaneo durante il commit della transazione.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> Qui `SaveChanges` viene richiamato con `acceptAllChangesOnSuccess` impostato su `false` per evitare di modificare lo stato dell' `Blog` entità in se ha `Unchanged` `SaveChanges` esito positivo. In questo modo è possibile ripetere la stessa operazione se il commit ha esito negativo e viene eseguito il rollback della transazione.

### <a name="option-4---manually-track-the-transaction"></a>Opzione 4: rilevare manualmente la transazione

Se è necessario usare chiavi generate dall'archivio o è necessario un metodo generico per gestire gli errori di commit che non dipendono dall'operazione eseguita, a ogni transazione potrebbe essere assegnato un ID verificato quando il commit non riesce.

1. Aggiungere una tabella al database utilizzato per tenere traccia dello stato delle transazioni.
2. Inserire una riga nella tabella all'inizio di ogni transazione.
3. Se la connessione non riesce durante il commit, verificare la presenza della riga corrispondente nel database.
4. Se il commit ha esito positivo, eliminare la riga corrispondente per evitare la crescita della tabella.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> Verificare che il contesto utilizzato per la verifica disponga di una strategia di esecuzione definita poiché è probabile che la connessione non riesca di nuovo durante la verifica in caso di errore durante il commit della transazione.
