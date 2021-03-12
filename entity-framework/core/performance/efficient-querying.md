---
title: Query efficienti-EF Core
description: Guida alle prestazioni per eseguire query efficienti tramite Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: e14837b779f2fbe8d5bf10206c6a336a952fc35b
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023874"
---
# <a name="efficient-querying"></a>Esecuzione di query efficienti

L'esecuzione di query in modo efficiente è un argomento vasto, che copre gli argomenti come indici di ampia portata, strategie di caricamento di entità correlate e molti altri. In questa sezione vengono illustrati alcuni temi comuni per velocizzare le query e si verificano gli errori riscontrati dagli utenti.

## <a name="use-indexes-properly"></a>Usa indici corretti

Il fattore decisivo principale nel determinare se una query viene eseguita velocemente o meno è se utilizzerà correttamente gli indici laddove appropriato: i database vengono in genere utilizzati per includere grandi quantità di dati e le query che attraversano intere tabelle sono in genere origini di gravi problemi di prestazioni. Non è facile individuare i problemi di indicizzazione, perché non è immediatamente ovvio se una query specifica utilizzerà o meno un indice. Ad esempio:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

Un modo efficace per individuare i problemi di indicizzazione consiste nel determinare prima una query lenta, quindi esaminarne il piano di query tramite lo strumento preferito del database. Per ulteriori informazioni su come eseguire questa operazione, vedere la pagina relativa alla [diagnosi delle prestazioni](xref:core/performance/performance-diagnosis) . Il piano di query indica se la query attraversa l'intera tabella oppure se utilizza un indice.

Come regola generale, non esiste alcuna conoscenza EF speciale per l'utilizzo di indici o la diagnosi dei problemi di prestazioni correlati; le informazioni generali sul database correlate agli indici sono rilevanti per le applicazioni EF, in quanto per le applicazioni che non usano EF. Di seguito sono elencate alcune linee guida generali da tenere presenti quando si usano gli indici:

* Sebbene gli indici accelerino le query, rallentano anche gli aggiornamenti poiché devono essere mantenuti aggiornati. Evitare di definire gli indici che non sono necessari e prendere in considerazione l'utilizzo di [filtri](xref:core/modeling/indexes#index-filter) per gli indici per limitare l'indice a un subset di righe, riducendo in tal modo questo overhead.
* Gli indici compositi possono velocizzare le query che filtrano in base a più colonne, ma possono anche velocizzare le query che non filtrano tutte le colonne dell'indice, a seconda dell'ordinamento. Ad esempio, un indice sulle colonne A e B velocizza le query che filtrano per A e B, oltre a filtrare le query solo per un, ma non velocizza il filtraggio delle query solo da B.
* Se una query Filtra in base a un'espressione su una colonna, ad esempio `price / 2` , non è possibile utilizzare un indice semplice. Tuttavia, è possibile definire una [colonna salvata](xref:core/modeling/generated-properties#computed-columns) in modo permanente per l'espressione e creare un indice su tale colonna. Alcuni database supportano anche gli indici delle espressioni, che possono essere usati direttamente per velocizzare le query che filtrano in base a qualsiasi espressione.
* Database diversi consentono di configurare gli indici in diversi modi e in molti casi EF Core provider li espongono tramite l'API Fluent. Il provider SQL Server, ad esempio, consente di configurare se un indice è di tipo [cluster](xref:core/providers/sql-server/indexes#clustering)o di impostarne il [fattore di riempimento](xref:core/providers/sql-server/indexes#fill-factor). Per ulteriori informazioni, consultare la documentazione del provider.

## <a name="project-only-properties-you-need"></a>Proprietà solo progetto necessarie

EF Core rende molto semplice eseguire una query sulle istanze di entità e quindi utilizzarle nel codice. Tuttavia, l'esecuzione di query sulle istanze di entità può eseguire spesso il pull di più dati del necessario dal database. Considerare quanto segue:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

Sebbene questo codice necessiti solo della proprietà di ogni blog `Url` , viene recuperata l'intera entità del Blog e le colonne non necessarie vengono trasferite dal database:

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

Questa operazione può essere ottimizzata usando `Select` per indicare a EF quali colonne si desidera proiettare:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

Il SQL risultante esegue il pull solo delle colonne necessarie:

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

Se è necessario proiettare più di una colonna, proiettare in un tipo anonimo C# con le proprietà desiderate.

Si noti che questa tecnica è molto utile per le query di sola lettura, ma gli elementi risultano più complessi se è necessario *aggiornare* i Blog recuperati, poiché il rilevamento delle modifiche di EF funziona solo con le istanze di entità. È possibile eseguire gli aggiornamenti senza caricare intere entità collegando un'istanza del Blog modificata e indicando a EF quali proprietà sono state modificate, ma si tratta di una tecnica più avanzata che potrebbe non essere utile.

## <a name="limit-the-resultset-size"></a>Limitare le dimensioni del set di risultati

Per impostazione predefinita, una query restituisce tutte le righe che corrispondono ai relativi filtri:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

Poiché il numero di righe restituite dipende dai dati effettivi presenti nel database, è impossibile conoscere la quantità di dati che verranno caricati dal database, la quantità di memoria che verrà assorbita dai risultati e il carico aggiuntivo che verrà generato durante l'elaborazione di questi risultati, ad esempio inviando i dati a un browser utente tramite la rete. Fondamentalmente, i database di test contengono spesso pochi dati, in modo che tutto funzioni correttamente durante i test, ma i problemi di prestazioni appaiono improvvisamente quando la query viene avviata in esecuzione su dati reali e vengono restituite molte righe.

Di conseguenza, in genere vale la pena pensare a limitare il numero di risultati:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

Come minimo, l'interfaccia utente potrebbe visualizzare un messaggio che indica che è possibile che esistano più righe nel database e che sia possibile recuperarle in un altro modo. Una soluzione completa implementa il *paging*, in cui l'interfaccia utente Mostra solo un determinato numero di righe alla volta e consente agli utenti di passare alla pagina successiva in base alle esigenze. Questa operazione combina in genere gli <xref:System.Linq.Enumerable.Take%2A> <xref:System.Linq.Enumerable.Skip%2A> operatori e per selezionare ogni volta un intervallo specifico nel set di risultati.

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>Evitare l'esplosione cartesiana durante il caricamento di entità correlate

Nei database relazionali tutte le entità correlate vengono caricate introducendo JOIN in una singola query.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog. Questa duplicazione porta al cosiddetto problema "esplosione cartesiana". Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione.

EF consente di evitare questo effetto tramite l'uso di "Split queries", che carica le entità correlate tramite query separate. Per ulteriori informazioni, leggere [la documentazione relativa alle query Split e single](xref:core/querying/single-split-queries).

> [!NOTE]
> L'implementazione corrente delle [query Split](xref:core/querying/single-split-queries) esegue un round trip per ogni query. Si prevede di migliorare questo in futuro ed eseguire tutte le query in un singolo round trip.

## <a name="load-related-entities-eagerly-when-possible"></a>Caricare le entità correlate con entusiasmo quando possibile

Prima di continuare con questa sezione, è consigliabile leggere [la pagina dedicata sulle entità correlate](xref:core/querying/related-data) .

Quando si gestiscono le entità correlate, in genere è possibile sapere in anticipo cosa è necessario caricare: un esempio tipico è il caricamento di un determinato set di Blog, insieme a tutti i post. In questi scenari è sempre preferibile usare il [caricamento eager](xref:core/querying/related-data/eager), in modo che EF possa recuperare tutti i dati necessari in un round trip. La funzionalità di [inclusione filtrata](xref:core/querying/related-data/eager#filtered-include) , introdotta in EF Core 5,0, consente inoltre di limitare le entità correlate che si desidera caricare, mantenendo al tempo stesso il processo di caricamento e quindi fattibile in un unico round trip:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

In altri scenari, è possibile che non si conosca quale entità correlata verrà necessaria prima di ottenere l'entità principale. Ad esempio, quando si carica un Blog, potrebbe essere necessario consultare un'altra origine dati, possibilmente un servizio Web, per sapere se sono interessati ai post del Blog. In questi casi, è possibile usare il caricamento [esplicito](xref:core/querying/related-data/explicit) o [Lazy](xref:core/querying/related-data/lazy) per recuperare separatamente le entità correlate e popolare la navigazione dei post del Blog. Si noti che poiché questi metodi non sono ansiosi, richiedono un round trip aggiuntivo al database, che è l'origine del rallentamento; a seconda dello scenario specifico, può essere più efficiente caricare sempre tutti i post, anziché eseguire le round trip aggiuntive e ottenere solo i post necessari.

### <a name="beware-of-lazy-loading"></a>Prestare attenzione al caricamento lazy

Il [caricamento lazy](xref:core/querying/related-data/lazy) spesso sembra un metodo molto utile per scrivere la logica del database, dal momento che EF Core carica automaticamente le entità correlate dal database man mano che si accede al codice. In questo modo si evita di caricare le entità correlate che non sono necessarie, ad esempio il [caricamento esplicito](xref:core/querying/related-data/explicit), ed è apparentemente possibile liberare il programmatore dalla necessità di gestire completamente le entità correlate. Tuttavia, il caricamento lazy è particolarmente soggetto alla produzione di round trip aggiuntivi non necessari che possono rallentare l'applicazione.

Considerare quanto segue:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

Questo frammento di codice apparentemente innocuo scorre tutti i Blog e i loro post, stamparli. L'attivazione della [registrazione delle istruzioni](xref:core/logging-events-diagnostics/index) di EF Core rivela quanto segue:

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

Che cosa succede qui? Perché tutte queste query vengono inviate per i cicli semplici sopra indicati? Con il caricamento lazy, i post di un blog vengono caricati solo quando viene eseguito l'accesso alla relativa proprietà post; di conseguenza, ogni iterazione nell'oggetto foreach interno attiva una query di database aggiuntiva in un round trip. Di conseguenza, dopo che la query iniziale ha caricato tutti i Blog, abbiamo un'altra query *per ogni Blog*, che carica tutti i post; Questo problema viene talvolta definito problema *N + 1* e può causare problemi di prestazioni molto significativi.

Supponendo che siano necessari tutti i post di Blog, è opportuno usare invece il caricamento eager. È possibile usare l'operatore di [inclusione](xref:core/querying/related-data/eager#eager-loading) per eseguire il caricamento, ma poiché sono necessari solo gli URL dei Blog (ed [è necessario caricare solo gli elementi necessari](xref:core/performance/efficient-querying#project-only-properties-you-need)). Quindi, usiamo una proiezione:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

In questo modo EF Core recuperare tutti i Blog, insieme ai rispettivi post, in una singola query. In alcuni casi può essere utile anche evitare effetti di esplosione cartesiana usando le [query Split](xref:core/querying/single-split-queries).

> [!WARNING]
> Poiché il caricamento lazy rende estremamente semplice attivare inavvertitamente il problema N + 1, è consigliabile evitarlo. Il caricamento immediato o esplicito ne rende molto chiaro il codice sorgente quando si verifica un round trip del database.

## <a name="buffering-and-streaming"></a>Buffering e streaming

Il buffering si riferisce al caricamento di tutti i risultati della query in memoria, mentre lo streaming significa che EF invia ogni volta all'applicazione un singolo risultato, senza mai contenere l'intero ResultSet in memoria. In linea di principio, i requisiti di memoria di una query di streaming sono corretti, ovvero se la query restituisce una riga o 1000; una query di buffering, d'altra parte, richiede una maggiore quantità di memoria e viene restituito un numero maggiore di righe. Per le query che generano set di risultati di grandi dimensioni, questo può essere un fattore importante per le prestazioni.

Il fatto che i flussi o i buffer delle query dipendano da come vengono valutati:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

Se le query restituiscono solo pochi risultati, probabilmente non è necessario preoccuparsi di questa operazione. Tuttavia, se la query potrebbe restituire un numero elevato di righe, è opportuno considerare lo streaming anziché il buffering.

> [!NOTE]
> Evitare <xref:System.Linq.Enumerable.ToList%2A> di utilizzare o <xref:System.Linq.Enumerable.ToArray%2A> se si desidera utilizzare un altro operatore LINQ nel risultato, in modo da memorizzare nel buffer tutti i risultati in memoria. In alternativa, utilizzare <xref:System.Linq.Enumerable.AsEnumerable%2A>.

### <a name="internal-buffering-by-ef"></a>Buffering interno di EF

In determinate situazioni, EF memorizza nel buffer il ResultSet internamente, indipendentemente dalla modalità di valutazione della query. I due casi in cui si verifica questo problema sono:

* Quando è attiva una strategia di esecuzione di nuovo tentativo. Questa operazione viene eseguita per assicurarsi che vengano restituiti gli stessi risultati se la query viene ritentata in un secondo momento.
* Quando si utilizza [Split query](xref:core/querying/single-split-queries) , i set di risultati di tutte le query tranne l'ultima vengono memorizzati nel buffer, a meno che Mars non sia abilitato in SQL Server. Questo perché in genere non è possibile avere più set di risultati di query attivi contemporaneamente.

Si noti che questo buffering interno si verifica in aggiunta a qualsiasi buffer causato tramite operatori LINQ. Se, ad esempio, si usa <xref:System.Linq.Enumerable.ToList%2A> in una query e viene eseguita una strategia di esecuzione ritentata, il ResultSet viene caricato in memoria *due volte*: una volta internamente da EF e una volta da <xref:System.Linq.Enumerable.ToList%2A> .

## <a name="tracking-no-tracking-and-identity-resolution"></a>Rilevamento, senza rilevamento e risoluzione di identità

Prima di continuare con questa sezione, è consigliabile leggere [la pagina dedicata su rilevamento e senza Tracking](xref:core/querying/tracking) .

EF tiene traccia delle istanze di entità per impostazione predefinita, in modo che le modifiche vengano rilevate e rese disponibili quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo. Un altro effetto delle query di rilevamento è che EF rileva se un'istanza è già stata caricata per i dati e restituirà automaticamente l'istanza rilevata anziché restituirne una nuova. Questa operazione è detta *risoluzione delle identità*. Dal punto di vista delle prestazioni, il rilevamento delle modifiche indica quanto segue:

* EF mantiene internamente un dizionario di istanze rilevate. Quando vengono caricati nuovi dati, EF controlla il dizionario per verificare se un'istanza è già registrata per la chiave di tale entità (risoluzione dell'identità). Quando si caricano i risultati della query, la manutenzione e le ricerche del dizionario riprendono tempo.
* Prima di passare un'istanza caricata all'applicazione, EF *snapshot* tale istanza e mantiene lo snapshot internamente. Quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo, l'istanza dell'applicazione viene confrontata con lo snapshot per individuare le modifiche da salvare in modo permanente. Lo snapshot occupa più memoria e il processo istantanee richiede tempo; a volte è possibile specificare un comportamento istantanee diverso, possibilmente più efficiente tramite gli [operatori di confronto dei valori](xref:core/modeling/value-comparers), o utilizzare proxy di rilevamento delle modifiche per ignorare completamente il processo istantanee (sebbene sia incluso in un proprio set di svantaggi).

Negli scenari di sola lettura in cui le modifiche non vengono salvate nel database, è possibile evitare i sovraccarichi sopra indicati usando [query senza rilevamento](xref:core/querying/tracking#no-tracking-queries). Tuttavia, poiché le query senza rilevamento non eseguono la risoluzione delle identità, una riga di database a cui si fa riferimento da più righe caricate verrà materializzata come istanze diverse.

Per illustrare, si supponga di caricare un numero elevato di post dal database, così come il Blog a cui viene fatto riferimento da ogni post. Se 100 post che fanno riferimento allo stesso Blog, una query di rilevamento rileva questo problema tramite la risoluzione di identità e tutte le istanze post faranno riferimento alla stessa istanza di Blog deduplicata. Una query senza rilevamento, al contrario, duplica lo stesso Blog 100 volte e il codice dell'applicazione deve essere scritto di conseguenza.

Di seguito sono riportati i risultati di un benchmark per il confronto tra rilevamento e nessun rilevamento per una query che carica 10 Blog con 20 post ciascuno. [Il codice sorgente è disponibile qui](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/QueryTrackingBehavior.cs)e può essere usato come base per le misurazioni.

|       Metodo | NumBlogs | NumPostsPerBlog |       Media |    Errore |   StdDev |     Mediana | Proporzioni | RatioSD |   Generazione 0 |   Generazione 1 | Generazione 2 | Allocato |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   AsTracking |       10 |              20 | 1.414,7 US | 27,20 US | 45,44 US | 1.405,5 US |  1,00 |    0,00 | 60,5469 | 13,6719 |     - | 380,11 KB |
| AsNoTracking |       10 |              20 |   993,3 US | 24,04 US | 65,40 US |   966,2 US |  0.71 |    0.05 | 37,1094 |  6,8359 |     - | 232,89 KB |

Infine, è possibile eseguire gli aggiornamenti senza l'overhead del rilevamento delle modifiche, utilizzando una query senza rilevamento e quindi collegando l'istanza restituita al contesto, specificando quali modifiche devono essere apportate. Questa operazione trasferisce il carico del rilevamento delle modifiche da EF all'utente e deve essere tentata solo se il sovraccarico del rilevamento delle modifiche è stato inaccettabile tramite la profilatura o il benchmarking.

## <a name="using-raw-sql"></a>Utilizzo di SQL non elaborato

In alcuni casi, SQL più ottimizzato esiste per la query, che EF non genera. Questo problema può verificarsi quando il costrutto SQL è un'estensione specifica del database non supportato o semplicemente perché EF non lo converte ancora. In questi casi, la scrittura manuale di SQL può offrire un notevole miglioramento delle prestazioni e EF supporta diversi modi per eseguire questa operazione.

* Usare SQL RAW [direttamente nella query](xref:core/querying/raw-sql), ad esempio tramite <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> . EF consente anche di comporre su SQL non elaborato con query LINQ normali, consentendo di esprimere solo una parte della query in SQL non elaborato. Si tratta di una tecnica efficace quando SQL non elaborato deve essere usato solo in una singola query nella codebase.
* Definire una [funzione definita dall'utente](xref:core/querying/database-functions) (UDF) e quindi chiamarla dalle query. Si noti che, a partire da 5,0, EF consente alle funzioni definite dall'utente di restituire set di risultati completi, ovvero funzioni con valori di tabella (funzioni con valori), e consente inoltre di eseguire il mapping di un oggetto `DbSet` a una funzione, rendendola simile a un'altra tabella.
* Definire una vista di database ed eseguire query su di essa nelle query. Si noti che, a differenza delle funzioni, le viste non accettano parametri.

> [!NOTE]
> In genere, SQL non elaborato deve essere usato come ultima risorsa, dopo aver verificato che EF non è in grado di generare il SQL desiderato e quando le prestazioni sono sufficientemente importanti per la giustificazione da parte della query specificata. L'uso di SQL non elaborato comporta svantaggi di manutenzione considerevoli.

## <a name="asynchronous-programming"></a>Programmazione asincrona

Come regola generale, per garantire la scalabilità dell'applicazione, è importante usare sempre le API asincrone anziché una sincrona, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> invece di <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Le API sincrone bloccano il thread per la durata dell'I/O del database, aumentando la necessità di thread e il numero di cambi di contesto del thread che devono verificarsi.

Per ulteriori informazioni, vedere la pagina relativa alla [programmazione asincrona](xref:core/miscellaneous/async).

> [!WARNING]
> Evitare di combinare codice sincrono e asincrono nella stessa applicazione: è molto facile attivare inavvertitamente problemi di inedia dei pool di thread.

## <a name="additional-resources"></a>Risorse aggiuntive

Per alcune procedure consigliate per il confronto dei valori nullable, vedere la [sezione prestazioni](xref:core/querying/null-comparisons#writing-performant-queries) della pagina della documentazione di confronto null.
