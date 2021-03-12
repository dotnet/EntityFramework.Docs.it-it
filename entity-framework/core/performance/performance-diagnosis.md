---
title: Diagnosi delle prestazioni-EF Core
description: Diagnosi delle prestazioni Entity Framework Core e identificazione dei colli di bottiglia
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 85ffd1826723ad97bdcce517781f920c193e4286
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023848"
---
# <a name="performance-diagnosis"></a>Diagnosi delle prestazioni

In questa sezione vengono descritte le modalità di rilevamento dei problemi di prestazioni nell'applicazione EF e una volta identificata un'area problematica, come analizzarla ulteriormente per identificare il problema principale. È importante diagnosticare e analizzare attentamente eventuali problemi prima di passare a qualsiasi conclusione ed evitare di presumere dove si trova la radice del problema.

## <a name="identifying-slow-database-commands-via-logging"></a>Identificazione di comandi di database lenti tramite registrazione

Alla fine della giornata, EF prepara ed esegue i comandi da eseguire nel database. con il database relazionale, ovvero l'esecuzione di istruzioni SQL tramite l'API del database ADO.NET. Se una determinata query sta richiedendo troppo tempo, ad esempio perché manca un indice, è possibile individuarlo esaminando i log di esecuzione dei comandi e osservando il tempo necessario.

EF rende molto semplice acquisire i tempi di esecuzione del comando tramite una [semplice registrazione](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging):

### <a name="simple-logging"></a>[Registrazione semplice](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

Quando il livello di registrazione è impostato su `LogLevel.Information` , EF genera un messaggio di log per ogni esecuzione del comando con il tempo impiegato:

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

Il comando precedente ha impiegato 4 millisecondi. Se un determinato comando richiede più del previsto, si è trovato un possibile colpevole per un problema di prestazioni e ora è possibile concentrarsi su di esso per capire il motivo per cui viene eseguito lentamente. La registrazione dei comandi può anche rivelare i casi in cui vengono eseguiti round trip di database imprevisti; Questa operazione viene visualizzata come più comandi in cui è previsto solo uno.

> [!WARNING]
> La registrazione dell'esecuzione dei comandi abilitata nell'ambiente di produzione non è in genere una buona idea. La registrazione rallenta l'applicazione e può creare rapidamente file di log di grandi dimensioni che possono riempire il disco del server. È consigliabile proseguire solo per un breve intervallo di tempo per raccogliere dati, monitorando con attenzione l'applicazione, oppure per acquisire i dati di registrazione in un sistema di pre-produzione.

## <a name="correlating-database-commands-to-linq-queries"></a>Correlazione di comandi di database a query LINQ

Un problema con la registrazione dell'esecuzione dei comandi è che talvolta è difficile correlare query SQL e query LINQ: i comandi SQL eseguiti da EF possono essere molto diversi dalle query LINQ da cui sono state generate. Per risolvere questo problema, è consigliabile usare la funzionalità [tag di query](xref:core/querying/tags) di EF, che consente di inserire un piccolo commento di identificazione nella query SQL:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Il tag viene visualizzato nei log:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Spesso è opportuno contrassegnare le query principali di un'applicazione in questo modo, per rendere i log di esecuzione dei comandi più immediatamente leggibili.

## <a name="other-interfaces-for-capturing-performance-data"></a>Altre interfacce per l'acquisizione dei dati sulle prestazioni

Esistono diverse alternative alla funzionalità di registrazione di EF per l'acquisizione dei tempi di esecuzione dei comandi, che potrebbero essere più potenti. I database sono in genere dotati di strumenti di analisi e analisi delle prestazioni, che in genere forniscono informazioni più dettagliate, specifiche del database, oltre ai semplici tempi di esecuzione. la configurazione, le funzionalità e l'utilizzo effettivi variano notevolmente tra i database.

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) è ad esempio un potente client in grado di connettersi all'istanza di SQL Server e fornire informazioni importanti sulla gestione e sulle prestazioni. Esula dall'ambito di questa sezione per approfondire i dettagli, ma due funzionalità che vale la pena citare sono [monitoraggio attività](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio), che fornisce un dashboard attivo dell'attività del server (incluse le query più dispendiose) e la funzionalità [degli eventi estesi (XEvent)](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) , che consente di definire sessioni di acquisizione dati arbitrarie che possono essere personalizzate in base alle esigenze specifiche. [La documentazione di SQL Server sul monitoraggio](/sql/relational-databases/performance/monitor-and-tune-for-performance) fornisce ulteriori informazioni su queste funzionalità e su altre.

Un altro approccio per l'acquisizione dei dati sulle prestazioni consiste nel raccogliere le informazioni emesse automaticamente da EF o dal driver di database tramite l' `DiagnosticSource` interfaccia, quindi analizzare i dati o visualizzarli in un dashboard. Se si usa Azure, [applicazione Azure Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) offre un monitoraggio efficace, integrando le prestazioni del database e i tempi di esecuzione delle query nell'analisi della velocità con cui vengono gestite le richieste Web. Altre informazioni su questo sono disponibili nell' [esercitazione sulle prestazioni Application Insights](/azure/azure-monitor/learn/tutorial-performance)e nella [pagina analisi SQL di Azure](/azure/azure-monitor/insights/azure-sql).

## <a name="inspecting-query-execution-plans"></a>Controllo dei piani di esecuzione delle query

Una volta individuata una query problematica che richiede l'ottimizzazione, il passaggio successivo consiste in genere nell'analisi del *piano di esecuzione* della query. Quando i database ricevono un'istruzione SQL, in genere producono un piano di come deve essere eseguito il piano; Questa operazione richiede a volte un processo decisionale complesso, in base al quale sono stati definiti gli indici, alla quantità di dati presenti nelle tabelle e così via. il piano deve essere in genere memorizzato nella cache del server per ottenere prestazioni ottimali. Nei database relazionali viene in genere fornito un modo per consentire agli utenti di visualizzare il piano di query, insieme al costo calcolato per parti diverse della query; Questa operazione è utile per migliorare le query.

Per iniziare a usare SQL Server, vedere la documentazione relativa ai [piani di esecuzione delle query](/sql/relational-databases/performance/execution-plans). Il flusso di lavoro di analisi tipico consiste nell'utilizzare [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan), incollando il SQL di una query lenta identificata tramite uno dei mezzi precedenti e [producendo un piano di esecuzione grafico](/sql/relational-databases/performance/display-an-actual-execution-plan):

![Visualizzazione di un piano di esecuzione SQL Server](_static/actualexecplan.png)

Sebbene i piani di esecuzione possano sembrare complicati inizialmente, vale la pena spendere una certa quantità di tempo per conoscerli. È particolarmente importante notare i costi associati a ogni nodo del piano e per identificare la modalità di utilizzo degli indici (o meno) nei vari nodi.

Sebbene le informazioni precedenti siano specifiche per SQL Server, gli altri database forniscono in genere lo stesso tipo di strumenti con una visualizzazione simile.

> [!IMPORTANT]
> I database a volte generano piani di query diversi a seconda dei dati effettivi nel database. Se, ad esempio, una tabella contiene solo poche righe, è possibile che un database scelga di non utilizzare un indice in tale tabella, ma per eseguire un'analisi completa della tabella. Se si analizzano i piani di query in un database di prova, verificare sempre che contenga dati simili al sistema di produzione.

## <a name="event-counters"></a>Contatori di eventi

Le sezioni precedenti incentrate su come ottenere informazioni sui comandi e sul modo in cui questi comandi vengono eseguiti nel database. Inoltre, EF espone un set di *contatori di eventi* che forniscono informazioni più di livello inferiore sulle operazioni eseguite all'interno di EF e su come viene usato dall'applicazione. Questi contatori possono essere molto utili per la diagnosi di problemi di prestazioni e di anomalie delle prestazioni specifici, ad esempio [problemi di memorizzazione nella cache delle query](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) che causano ricompilazioni costanti, perdite di DbContext non disdisposte e altre ancora.

Per ulteriori informazioni, vedere la pagina dedicata sui [contatori degli eventi di EF](xref:core/logging-events-diagnostics/event-counters) .

## <a name="benchmarking-with-ef-core"></a>Benchmarking con EF Core

Alla fine della giornata, a volte è necessario sapere se un particolare metodo di scrittura o di esecuzione di una query è più veloce rispetto a un altro. È importante non presupporre o speculare la risposta ed è estremamente semplice mettere insieme un benchmark rapido per ottenere la risposta. Quando si scrivono i benchmark, è consigliabile usare la nota libreria [BenchmarkDotNet](https://benchmarkdotnet.org/index.html) , che gestisce molti problemi riscontrati dagli utenti durante il tentativo di scrivere i propri benchmark: sono state eseguite alcune iterazioni di riscaldamento? Quante iterazioni eseguono effettivamente il benchmark e perché? Verrà ora esaminato il benchmark con EF Core.

> [!TIP]
> Il progetto di benchmark completo per l'origine seguente è disponibile [qui](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/AverageBlogRanking.cs). Si consiglia di copiarlo e usarlo come modello per i propri benchmark.

Come semplice scenario di benchmark, è possibile confrontare i seguenti metodi diversi per calcolare la classificazione media di tutti i Blog nel database:

* Caricare tutte le entità, sommare le rispettive classificazioni e calcolare la media.
* Analogamente a quanto sopra, utilizzare solo una query senza rilevamento. Questa operazione dovrebbe essere più veloce, perché la risoluzione delle identità non viene eseguita e le entità non sono snapshotted ai fini del rilevamento delle modifiche.
* Evitare di caricare le intere istanze di entità del Blog, proiettando solo la classificazione. Il non consente di trasferire le altre colonne non necessarie del tipo di entità Blog.
* Calcolare la media nel database facendola parte della query. Questo è il modo più veloce, poiché tutto viene calcolato nel database e solo il risultato viene trasferito al client.

Con BenchmarkDotNet è possibile scrivere il codice per eseguire il benchmarking come metodo semplice, proprio come un unit test e BenchmarkDotNet esegue automaticamente ogni metodo per un numero sufficiente di iterazioni, misurando in modo affidabile il tempo necessario e la quantità di memoria allocata. Ecco il metodo diverso ([il codice di benchmark completo può essere visualizzato qui](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/AverageBlogRanking.cs)):

### <a name="load-entities"></a>[Carica entità](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[Carica entità, nessun rilevamento](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[Classificazione solo progetto](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[Calcola nel database](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

I risultati sono riportati di seguito, come stampato da BenchmarkDotNet:

|                 Metodo |       Media |    Errore |   StdDev |     Mediana | Proporzioni | RatioSD |    Generazione 0 |   Generazione 1 | Generazione 2 |  Allocato |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           LoadEntities | 2.860,4 US | 54,31 US | 93,68 US | 2.844,5 US |  4.55 |    0,33 | 210,9375 | 70,3125 |     - | 1309,56 KB |
| LoadEntitiesNoTracking | 1.353,0 US | 21,26 US | 18,85 US | 1.355,6 US |  2.10 |    0.14 |  87,8906 |  3,9063 |     - |  540,09 KB |
|     ProjectOnlyRanking |   910,9 US | 20,91 US | 61,65 US |   892,9 US |  1,46 |    0.14 |  41,0156 |  0,9766 |     - |  252,08 KB |
|    CalculateInDatabase |   627,1 US | 14,58 US | 42,54 US |   626,4 US |  1,00 |    0,00 |   4,8828 |       - |     - |   33,27 KB |

> [!NOTE]
> Poiché i metodi creano ed eliminano il contesto all'interno del metodo, queste operazioni vengono conteggiate per il benchmark, sebbene non facciano parte del processo di query. Questa operazione non è importante se l'obiettivo è confrontare due alternative tra loro (poiché la creazione di un'istanza e l'eliminazione del contesto sono le stesse) e fornisce una misura più olistica per l'intera operazione.

Una limitazione di BenchmarkDotNet è che consente di misurare le prestazioni semplici a thread singolo dei metodi forniti e pertanto non è particolarmente adatto per il benchmarking degli scenari simultanei.

> [!IMPORTANT]
> Assicurarsi sempre che nel database siano presenti dati simili ai dati di produzione durante il benchmarking. in caso contrario, i risultati del benchmark potrebbero non rappresentare le prestazioni effettive nell'ambiente di produzione.
