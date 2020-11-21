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
# <a name="event-counters"></a><span data-ttu-id="7c53a-103">Contatori di eventi</span><span class="sxs-lookup"><span data-stu-id="7c53a-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="7c53a-104">Questa funzionalità è stata aggiunta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="7c53a-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="7c53a-105">Entity Framework Core (EF Core) espone metriche numeriche continue che possono fornire un'indicazione corretta dell'integrità del programma.</span><span class="sxs-lookup"><span data-stu-id="7c53a-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="7c53a-106">Queste metriche possono essere usate per gli scopi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7c53a-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="7c53a-107">Tenere traccia del carico generale del database in tempo reale durante l'esecuzione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="7c53a-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="7c53a-108">Esporre procedure di codifica problematiche che possono causare un calo delle prestazioni</span><span class="sxs-lookup"><span data-stu-id="7c53a-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="7c53a-109">Rilevare e isolare il comportamento anomalo del programma</span><span class="sxs-lookup"><span data-stu-id="7c53a-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="7c53a-110">EF Core segnala le metriche tramite la funzionalità standard dei contatori degli eventi .NET. per una rapida panoramica del funzionamento dei contatori, è consigliabile leggere [questo post di Blog](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) .</span><span class="sxs-lookup"><span data-stu-id="7c53a-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="7c53a-111">Connettersi a un processo con i contatori DotNet</span><span class="sxs-lookup"><span data-stu-id="7c53a-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="7c53a-112">Lo [strumento DotNet-counts](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) può essere usato per connettersi a un processo in esecuzione e segnalare regolarmente EF Core contatori degli eventi; non è necessario eseguire alcuna operazione speciale nel programma perché questi contatori siano disponibili.</span><span class="sxs-lookup"><span data-stu-id="7c53a-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="7c53a-113">Installare prima di tutto lo `dotnet-counters` strumento: `dotnet tool install --global dotnet-counters` .</span><span class="sxs-lookup"><span data-stu-id="7c53a-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="7c53a-114">Individuare quindi l'ID processo (PID) del processo .NET che esegue l'applicazione EF Core:</span><span class="sxs-lookup"><span data-stu-id="7c53a-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="7c53a-115">Windows</span><span class="sxs-lookup"><span data-stu-id="7c53a-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="7c53a-116">Aprire Gestione attività di Windows facendo clic con il pulsante destro del mouse sulla barra delle applicazioni e scegliendo "Gestione attività".</span><span class="sxs-lookup"><span data-stu-id="7c53a-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="7c53a-117">Assicurarsi che l'opzione "altri dettagli" sia selezionata nella parte inferiore della finestra.</span><span class="sxs-lookup"><span data-stu-id="7c53a-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="7c53a-118">Nella scheda processi, fare clic con il pulsante destro del mouse su una colonna e verificare che la colonna PID sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="7c53a-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="7c53a-119">Individuare l'applicazione nell'elenco dei processi e ottenere l'ID del processo dalla colonna PID.</span><span class="sxs-lookup"><span data-stu-id="7c53a-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="7c53a-120">Linux o macOS</span><span class="sxs-lookup"><span data-stu-id="7c53a-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="7c53a-121">Usare il `ps` comando per elencare tutti i processi in esecuzione per l'utente.</span><span class="sxs-lookup"><span data-stu-id="7c53a-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="7c53a-122">Individuare l'applicazione nell'elenco dei processi e ottenere l'ID del processo dalla colonna PID.</span><span class="sxs-lookup"><span data-stu-id="7c53a-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="7c53a-123">All'interno dell'applicazione .NET l'ID del processo è disponibile come. `Process.GetCurrentProcess().Id` questa operazione può essere utile per la stampa del PID all'avvio.</span><span class="sxs-lookup"><span data-stu-id="7c53a-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="7c53a-124">Infine, avviare `dotnet-counters` come segue:</span><span class="sxs-lookup"><span data-stu-id="7c53a-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="7c53a-125">`dotnet-counters` Ora si connetterà al processo in esecuzione e inizierà a segnalare i dati del contatore continuo:</span><span class="sxs-lookup"><span data-stu-id="7c53a-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

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

## <a name="counters-and-their-meaning"></a><span data-ttu-id="7c53a-126">Contatori e relativo significato</span><span class="sxs-lookup"><span data-stu-id="7c53a-126">Counters and their meaning</span></span>

<span data-ttu-id="7c53a-127">Nome contatore</span><span class="sxs-lookup"><span data-stu-id="7c53a-127">Counter name</span></span>                          | <span data-ttu-id="7c53a-128">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7c53a-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="7c53a-129">Oggetti DbContext attivo</span><span class="sxs-lookup"><span data-stu-id="7c53a-129">Active DbContexts</span></span>                     | <span data-ttu-id="7c53a-130">Numero di istanze di DbContext attive e non disdisposte attualmente presenti nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7c53a-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="7c53a-131">Se questo numero cresce continuamente, è possibile che si verifichi una perdita perché le istanze di DbContext non vengono eliminate correttamente.</span><span class="sxs-lookup"><span data-stu-id="7c53a-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="7c53a-132">Si noti che se la funzionalità di [pooling del contesto](xref:core/miscellaneous/context-pooling) è abilitata, questo numero include le istanze di DbContext in pool non attualmente in uso.</span><span class="sxs-lookup"><span data-stu-id="7c53a-132">Note that if [context pooling](xref:core/miscellaneous/context-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="7c53a-133">Errori dell'operazione di strategia di esecuzione</span><span class="sxs-lookup"><span data-stu-id="7c53a-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="7c53a-134">Il numero di volte in cui l'esecuzione di un'operazione sul database non è riuscita.</span><span class="sxs-lookup"><span data-stu-id="7c53a-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="7c53a-135">Se è abilitata una strategia di esecuzione di ripetizione dei tentativi, questo include ogni singolo errore all'interno di più tentativi nella stessa operazione.</span><span class="sxs-lookup"><span data-stu-id="7c53a-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="7c53a-136">Questo può essere usato per rilevare problemi temporanei con l'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="7c53a-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="7c53a-137">Errori di concorrenza ottimistica</span><span class="sxs-lookup"><span data-stu-id="7c53a-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="7c53a-138">Il numero di volte `SaveChanges` che non è riuscito a causa di un errore di concorrenza ottimistica, perché i dati nell'archivio dati sono stati modificati dopo il caricamento del codice.</span><span class="sxs-lookup"><span data-stu-id="7c53a-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="7c53a-139">Corrisponde a un oggetto <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> generato.</span><span class="sxs-lookup"><span data-stu-id="7c53a-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="7c53a-140">Query</span><span class="sxs-lookup"><span data-stu-id="7c53a-140">Queries</span></span>                               | <span data-ttu-id="7c53a-141">Numero di query eseguite.</span><span class="sxs-lookup"><span data-stu-id="7c53a-141">The number of queries executed.</span></span>
<span data-ttu-id="7c53a-142">Percentuale riscontri cache query (%)</span><span class="sxs-lookup"><span data-stu-id="7c53a-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="7c53a-143">Rapporto tra i riscontri nella cache di query e i mancati riscontri.</span><span class="sxs-lookup"><span data-stu-id="7c53a-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="7c53a-144">La prima volta che una query LINQ specificata viene eseguita da EF Core (esclusi i parametri), è necessario compilarla in un processo relativamente intenso.</span><span class="sxs-lookup"><span data-stu-id="7c53a-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="7c53a-145">In un'applicazione normale, tutte le query vengono riutilizzate e la percentuale di riscontri della cache delle query deve essere stabile al 100% dopo un periodo di riscaldamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="7c53a-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="7c53a-146">Se questo numero è inferiore al 100% nel tempo, è possibile che si verifichi un peggioramento delle prestazioni a causa di compilazioni ripetute, che potrebbero essere il risultato della generazione di query dinamiche non ottimali.</span><span class="sxs-lookup"><span data-stu-id="7c53a-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="7c53a-147">Salvataggio di modifiche</span><span class="sxs-lookup"><span data-stu-id="7c53a-147">SaveChanges</span></span>                           | <span data-ttu-id="7c53a-148">Numero di volte in cui `SaveChanges` è stato chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="7c53a-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="7c53a-149">Si noti che `SaveChanges` Salva più modifiche in un singolo batch, in modo che non rappresenti necessariamente ogni singolo aggiornamento eseguito su una singola entità.</span><span class="sxs-lookup"><span data-stu-id="7c53a-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c53a-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7c53a-150">Additional resources</span></span>

* [<span data-ttu-id="7c53a-151">Documentazione di .NET sui contatori di eventi</span><span class="sxs-lookup"><span data-stu-id="7c53a-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
