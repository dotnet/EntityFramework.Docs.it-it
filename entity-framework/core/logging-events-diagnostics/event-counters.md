---
title: Contatori eventi-EF Core
description: Verifica EF Core prestazioni e diagnosi delle anomalie con i contatori di eventi .NET
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 46acfe82d8aeb7d16146bae0cc2cd4ff733e2831
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003705"
---
# <a name="event-counters"></a>Contatori di eventi

> [!NOTE]
> Questa funzionalità è stata aggiunta in EF Core 5,0.

Entity Framework Core (EF Core) espone metriche numeriche continue che possono fornire un'indicazione corretta dell'integrità del programma. Queste metriche possono essere usate per gli scopi seguenti:

* Tenere traccia del carico generale del database in tempo reale durante l'esecuzione dell'applicazione
* Esporre procedure di codifica problematiche che possono causare un calo delle prestazioni
* Rilevare e isolare il comportamento anomalo del programma

EF Core segnala le metriche tramite la funzionalità standard dei contatori degli eventi .NET. per una rapida panoramica del funzionamento dei contatori, è consigliabile leggere [questo post di Blog](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) .

## <a name="attach-to-a-process-using-dotnet-counters"></a>Connettersi a un processo con i contatori DotNet

Lo [strumento DotNet-counts](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) può essere usato per connettersi a un processo in esecuzione e segnalare regolarmente EF Core contatori degli eventi; non è necessario eseguire alcuna operazione speciale nel programma perché questi contatori siano disponibili.

Installare prima di tutto lo `dotnet-counters` strumento: `dotnet tool install --global dotnet-counters` .

Individuare quindi l'ID processo (PID) del processo .NET che esegue l'applicazione EF Core:

### <a name="windows"></a>[Windows](#tab/windows)

1. Aprire Gestione attività di Windows facendo clic con il pulsante destro del mouse sulla barra delle applicazioni e scegliendo "Gestione attività".
2. Assicurarsi che l'opzione "altri dettagli" sia selezionata nella parte inferiore della finestra.
3. Nella scheda processi, fare clic con il pulsante destro del mouse su una colonna e verificare che la colonna PID sia abilitata.
4. Individuare l'applicazione nell'elenco dei processi e ottenere l'ID del processo dalla colonna PID.

### <a name="linux-or-macos"></a>[Linux o macOS](#tab/fluent-api)

1. Usare il `ps` comando per elencare tutti i processi in esecuzione per l'utente.
2. Individuare l'applicazione nell'elenco dei processi e ottenere l'ID del processo dalla colonna PID.

***

All'interno dell'applicazione .NET l'ID del processo è disponibile come. `Process.GetCurrentProcess().Id` questa operazione può essere utile per la stampa del PID all'avvio.

Infine, avviare `dotnet-counters` come segue:

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` Ora si connetterà al processo in esecuzione e inizierà a segnalare i dati del contatore continuo:

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a>Contatori e relativo significato

Nome contatore                          | Descrizione
------------------------------------- | ----
Oggetti DbContext attivo                     | Numero di istanze di DbContext attive e non disdisposte attualmente presenti nell'applicazione. Se questo numero cresce continuamente, è possibile che si verifichi una perdita perché le istanze di DbContext non vengono eliminate correttamente. Si noti che se la funzionalità di [pooling del contesto](xref:core/miscellaneous/context-pooling) è abilitata, questo numero include le istanze di DbContext in pool non attualmente in uso.
Errori dell'operazione di strategia di esecuzione | Il numero di volte in cui l'esecuzione di un'operazione sul database non è riuscita. Se è abilitata una strategia di esecuzione di ripetizione dei tentativi, questo include ogni singolo errore all'interno di più tentativi nella stessa operazione. Questo può essere usato per rilevare problemi temporanei con l'infrastruttura.
Errori di concorrenza ottimistica       | Il numero di volte `SaveChanges` che non è riuscito a causa di un errore di concorrenza ottimistica, perché i dati nell'archivio dati sono stati modificati dopo il caricamento del codice. Corrisponde a un oggetto <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> generato.
Query                               | Numero di query eseguite.
Percentuale riscontri cache query (%)              | Rapporto tra i riscontri nella cache di query e i mancati riscontri. La prima volta che una query LINQ specificata viene eseguita da EF Core (esclusi i parametri), è necessario compilarla in un processo relativamente intenso. In un'applicazione normale, tutte le query vengono riutilizzate e la percentuale di riscontri della cache delle query deve essere stabile al 100% dopo un periodo di riscaldamento iniziale. Se questo numero è inferiore al 100% nel tempo, è possibile che si verifichi un peggioramento delle prestazioni a causa di compilazioni ripetute, che potrebbero essere il risultato della generazione di query dinamiche non ottimali.
Salvataggio di modifiche                           | Numero di volte in cui `SaveChanges` è stato chiamato il metodo. Si noti che `SaveChanges` Salva più modifiche in un singolo batch, in modo che non rappresenti necessariamente ogni singolo aggiornamento eseguito su una singola entità.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Documentazione di .NET sui contatori di eventi](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
