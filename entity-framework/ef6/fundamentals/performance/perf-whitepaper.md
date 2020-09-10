---
title: Considerazioni sulle prestazioni per EF4, EF5 e EF6-EF6
description: Considerazioni sulle prestazioni per Entity Framework 4, 5 e 6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
uid: ef6/fundamentals/performance/perf-whitepaper
ms.openlocfilehash: 9d70eab61caace02f59f3c555ef416c45d4f8f45
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616170"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Considerazioni sulle prestazioni per EF 4, 5 e 6
Di David Obando, Eric Dettinger e altri

Pubblicato: aprile 2012

Ultimo aggiornamento: maggio 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introduzione

I Framework di mapping relazionale a oggetti costituiscono un modo pratico per fornire un'astrazione per l'accesso ai dati in un'applicazione orientata a oggetti. Per le applicazioni .NET, il O/RM consigliato da Microsoft è Entity Framework. Con qualsiasi astrazione, tuttavia, le prestazioni possono diventare un problema.

Questo white paper è stato scritto per illustrare le considerazioni sulle prestazioni durante lo sviluppo di applicazioni con Entity Framework, per offrire agli sviluppatori un'idea degli algoritmi interni Entity Framework che possono influire sulle prestazioni e fornire suggerimenti per l'analisi e il miglioramento delle prestazioni nelle applicazioni che utilizzano Entity Framework. Sono disponibili numerosi argomenti utili sulle prestazioni già disponibili sul Web e si è tentato di puntare a queste risorse laddove possibile.

Le prestazioni sono un argomento complesso. Questo white paper è concepito come una risorsa che consente di prendere decisioni relative alle prestazioni per le applicazioni che usano Entity Framework. Sono state incluse alcune metriche di test per dimostrare le prestazioni, ma queste metriche non sono destinate a indicatori assoluti delle prestazioni che verranno visualizzate nell'applicazione.

Per scopi pratici, questo documento presuppone che Entity Framework 4 venga eseguito in .NET 4,0 e Entity Framework 5 e 6 vengano eseguiti in .NET 4,5. Molti dei miglioramenti apportati alle prestazioni per Entity Framework 5 risiedono all'interno dei componenti di base forniti con .NET 4,5.

Entity Framework 6 è una versione fuori banda e non dipende dai componenti Entity Framework forniti con .NET. Entity Framework 6 funzionano sia in .NET 4,0 che in .NET 4,5 e possono offrire un notevole vantaggio a livello di prestazioni a coloro che non hanno eseguito l'aggiornamento da .NET 4,0, ma che desiderano i Entity Framework bit più recenti nella propria applicazione. Quando questo documento cita Entity Framework 6, si riferisce alla versione più recente disponibile al momento della stesura di questo documento: versione 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. esecuzione a freddo e a caldo della query

La prima volta che si esegue una query su un determinato modello, il Entity Framework esegue una grande quantità di lavoro dietro le quinte per caricare e convalidare il modello. Spesso si fa riferimento a questa prima query come query "a freddo".Ulteriori query su un modello già caricato sono note come query "calde" e sono molto più veloci.

Si prenda in considerazione una panoramica di alto livello del tempo impiegato per l'esecuzione di una query con Entity Framework e per vedere dove si stanno migliorando i Entity Framework 6.

**Prima esecuzione query-query a freddo**

| Scritture utente codice                                                                                     | Azione                    | Effetti sulle prestazioni di EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Effetti sulle prestazioni di EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Effetti sulle prestazioni di EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creazione del contesto          | Livello medio                                                                                                                                                                                                                                                                                                                                                                                                                        | Medio                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creazione di espressioni di query | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                           | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Esecuzione di query LINQ      | -Caricamento dei metadati: elevato ma memorizzato nella cache <br/> -Generazione di visualizzazioni: potenzialmente molto elevata ma memorizzata nella cache <br/> -Valutazione parametri: media <br/> -Conversione query: media <br/> -Generazione del materializzatore: media ma memorizzata nella cache <br/> -Esecuzione di query sul database: potenzialmente elevata <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione oggetti: media <br/> -Ricerca identità: media | -Caricamento dei metadati: elevato ma memorizzato nella cache <br/> -Generazione di visualizzazioni: potenzialmente molto elevata ma memorizzata nella cache <br/> -Valutazione parametri: bassa <br/> -Conversione query: media ma memorizzata nella cache <br/> -Generazione del materializzatore: media ma memorizzata nella cache <br/> -Esecuzione di query sul database: potenzialmente elevata (query migliori in alcune situazioni) <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione oggetti: media <br/> -Ricerca identità: media | -Caricamento dei metadati: elevato ma memorizzato nella cache <br/> -Generazione di visualizzazioni: media ma memorizzata nella cache <br/> -Valutazione parametri: bassa <br/> -Conversione query: media ma memorizzata nella cache <br/> -Generazione del materializzatore: media ma memorizzata nella cache <br/> -Esecuzione di query sul database: potenzialmente elevata (query migliori in alcune situazioni) <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione degli oggetti: media (più veloce di EF5) <br/> -Ricerca identità: media |
| `}`                                                                                                  | Connection. Close          | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                           | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Seconda esecuzione della query: query warm**

| Scritture utente codice                                                                                     | Azione                    | Effetti sulle prestazioni di EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Effetti sulle prestazioni di EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Effetti sulle prestazioni di EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creazione del contesto          | Livello medio                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medio                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creazione di espressioni di query | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Esecuzione di query LINQ      | -Ricerca di ~~caricamento~~ dei metadati: ~~alta ma memorizzata nella cache~~ -basso <br/> -Visualizza ricerca ~~generazione~~ : ~~potenzialmente molto elevata ma con memorizzazione nella cache~~ bassa <br/> -Valutazione parametri: media <br/> -Ricerca ~~conversione~~ query: media <br/> -Ricerca di ~~generazione~~ del materializzatore: ~~medio ma memorizzato nella cache~~ -basso <br/> -Esecuzione di query sul database: potenzialmente elevata <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione oggetti: media <br/> -Ricerca identità: media | -Ricerca di ~~caricamento~~ dei metadati: ~~alta ma memorizzata nella cache~~ -basso <br/> -Visualizza ricerca ~~generazione~~ : ~~potenzialmente molto elevata ma con memorizzazione nella cache~~ bassa <br/> -Valutazione parametri: bassa <br/> -Ricerca di ~~conversione~~ delle query: ~~media ma con memorizzazione nella cache~~ bassa <br/> -Ricerca di ~~generazione~~ del materializzatore: ~~medio ma memorizzato nella cache~~ -basso <br/> -Esecuzione di query sul database: potenzialmente elevata (query migliori in alcune situazioni) <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione oggetti: media <br/> -Ricerca identità: media | -Ricerca di ~~caricamento~~ dei metadati: ~~alta ma memorizzata nella cache~~ -basso <br/> -Visualizza ricerca ~~generazione~~ : ~~medio ma memorizzato nella cache~~ -basso <br/> -Valutazione parametri: bassa <br/> -Ricerca di ~~conversione~~ delle query: ~~media ma con memorizzazione nella cache~~ bassa <br/> -Ricerca di ~~generazione~~ del materializzatore: ~~medio ma memorizzato nella cache~~ -basso <br/> -Esecuzione di query sul database: potenzialmente elevata (query migliori in alcune situazioni) <br/> + Connessione. Apri <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materializzazione degli oggetti: media (più veloce di EF5) <br/> -Ricerca identità: media |
| `}`                                                                                                  | Connection. Close          | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bassa                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Esistono diversi modi per ridurre il costo delle prestazioni di query sia a freddo che a caldo, che verranno esaminati nella sezione seguente. In particolare, si esaminerà la riduzione dei costi del caricamento del modello nelle query a freddo usando visualizzazioni pregenerate, che consentono di ridurre i problemi di prestazioni riscontrati durante la generazione della visualizzazione. Per le query a caldo, verrà illustrata la memorizzazione nella cache del piano di query, nessuna query di rilevamento e diverse opzioni di esecuzione delle query.

### <a name="21-what-is-view-generation"></a>2,1 che cos'è la generazione di viste?

Per comprendere la generazione di visualizzazioni, è prima di tutto necessario comprendere quali sono le "viste di mapping". Le visualizzazioni di mapping sono rappresentazioni eseguibili delle trasformazioni specificate nel mapping per ogni set di entità e associazione. Internamente, queste viste di mapping hanno la forma di CQTs (alberi delle query canoniche). Esistono due tipi di visualizzazioni di mapping:

-   Visualizzazioni di query: rappresentano la trasformazione necessaria per passare dallo schema del database al modello concettuale.
-   Update views: rappresentano la trasformazione necessaria per passare dal modello concettuale allo schema del database.

Tenere presente che il modello concettuale potrebbe differire dallo schema del database in vari modi. Ad esempio, è possibile usare una singola tabella per archiviare i dati per due tipi di entità diversi. I mapping di ereditarietà e non banale svolgono un ruolo nella complessità delle visualizzazioni di mapping.

Il processo di calcolo di queste visualizzazioni in base alla specifica del mapping è quello che viene chiamato generazione viste. La generazione della vista può essere eseguita in modo dinamico quando viene caricato un modello o in fase di compilazione usando "visualizzazioni pregenerate"; quest'ultimo viene serializzato sotto forma di Entity SQL istruzioni in un \# file C o VB.

Vengono convalidate anche le visualizzazioni generate. Dal punto di vista delle prestazioni, la maggior parte del costo della generazione di visualizzazioni è in realtà la convalida delle visualizzazioni, che garantisce che le connessioni tra le entità abbiano senso e abbiano la cardinalità corretta per tutte le operazioni supportate.

Quando viene eseguita una query su un set di entità, la query viene combinata con la visualizzazione di query corrispondente e il risultato di questa composizione viene eseguito tramite il compilatore del piano per creare la rappresentazione della query che l'archivio di backup può comprendere. Per SQL Server, il risultato finale di questa compilazione sarà un'istruzione T-SQL SELECT. La prima volta che si esegue un aggiornamento su un set di entità, la vista aggiornamento viene eseguita tramite un processo simile per trasformarla in istruzioni DML per il database di destinazione.

### <a name="22-factors-that-affect-view-generation-performance"></a>2,2 fattori che influiscono sulle prestazioni della generazione di visualizzazioni

Le prestazioni del passaggio di generazione della vista non solo dipendono dalle dimensioni del modello, ma anche dalla modalità di interconnessione del modello. Se due entità sono connesse tramite una catena di ereditarietà o un'associazione, viene detto che sono connesse. Analogamente, se due tabelle sono connesse tramite una chiave esterna, sono connesse. Man mano che aumenta il numero di tabelle e entità connesse negli schemi, aumenta il costo della generazione della vista.

L'algoritmo utilizzato per generare e convalidare le visualizzazioni è esponenziale nel peggiore dei casi, sebbene vengano utilizzate alcune ottimizzazioni per migliorare questa situazione. I fattori più importanti che sembrano influire negativamente sulle prestazioni sono:

-   Dimensioni del modello, che fanno riferimento al numero di entità e alla quantità di associazioni tra queste entità.
-   Complessità del modello, in particolare l'ereditarietà che interessa un numero elevato di tipi.
-   Uso di associazioni indipendenti, anziché associazioni di chiavi esterne.

Per piccoli modelli semplici, il costo può essere sufficientemente piccolo da non interferire con le visualizzazioni generate in precedenza. Con l'aumentare delle dimensioni e della complessità del modello, sono disponibili diverse opzioni per ridurre i costi di generazione e convalida della visualizzazione.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2,3 utilizzo di visualizzazioni pregenerate per ridurre il tempo di caricamento del modello

Per informazioni dettagliate su come usare le visualizzazioni generate in precedenza in Entity Framework 6, vedere [viste di mapping predefinite](xref:ef6/fundamentals/performance/pre-generated-views)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 visualizzazioni pre-generate con Entity Framework Power Tools Community Edition

È possibile usare il [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) per generare visualizzazioni di modelli EDMX e Code First facendo clic con il pulsante destro del mouse sul file di classe del modello e usando il menu Entity Framework per selezionare "genera visualizzazioni". I Entity Framework Power Tools Community Edition funzionano solo con i contesti derivati da DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 come usare le visualizzazioni generate in precedenza con un modello creato da EDMGen

EDMGen è un'utilità fornita con .NET e funziona con Entity Framework 4 e 5, ma non con Entity Framework 6. EDMGen consente di generare un file di modello, il livello oggetti e le visualizzazioni dalla riga di comando. Uno degli output sarà un file di viste nel linguaggio preferito, VB o C \# . Si tratta di un file di codice contenente Entity SQL frammenti per ogni set di entità. Per abilitare le visualizzazioni generate in precedenza, è sufficiente includere il file nel progetto.

Se si apportano manualmente modifiche ai file di schema per il modello, sarà necessario rigenerare il file delle visualizzazioni. Per eseguire questa operazione, è possibile eseguire EDMGen con il flag **/mode: ViewGeneration** .

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 come usare le visualizzazioni generate in precedenza con un file EDMX

È anche possibile usare EDMGen per generare visualizzazioni per un file EDMX. l'argomento di MSDN a cui si fa riferimento in precedenza descrive come aggiungere un evento di pre-compilazione a tale scopo, ma questo è complesso e in alcuni casi non è possibile. In genere, è più semplice usare un modello T4 per generare le visualizzazioni quando il modello è in un file edmx.

Il Blog del team di ADO.NET include un post che descrive come usare un modello T4 per la generazione di visualizzazioni ( \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation> ). Questo post include un modello che può essere scaricato e aggiunto al progetto. Il modello è stato scritto per la prima versione di Entity Framework, quindi non è garantito che funzioni con le versioni più recenti di Entity Framework. Tuttavia, è possibile scaricare un set più aggiornato di modelli di generazione delle visualizzazioni per Entity Framework 4 e 5from in Visual Studio Gallery:

-   VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C \# : \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Se si usa Entity Framework 6 è possibile ottenere i modelli T4 per la generazione di visualizzazioni da Visual Studio Gallery all'indirizzo \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f> .

### <a name="24-reducing-the-cost-of-view-generation"></a>2,4 riduzione del costo della generazione di visualizzazioni

L'utilizzo di visualizzazioni pregenerate consente di spostare il costo della generazione di visualizzazioni dal caricamento del modello (tempo di esecuzione) al momento della progettazione. Sebbene questo miglioramento migliori le prestazioni di avvio in fase di esecuzione, si verificherà comunque il dolore della generazione di visualizzazioni durante lo sviluppo. Ci sono diversi trucchi aggiuntivi che consentono di ridurre il costo di generazione delle visualizzazioni, sia in fase di compilazione che in fase di esecuzione.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 utilizzo delle associazioni di chiavi esterne per ridurre i costi di generazione delle visualizzazioni

Sono stati rilevati diversi casi in cui il cambio di associazioni nel modello da associazioni indipendenti a associazioni di chiavi esterne ha migliorato notevolmente il tempo impiegato per la generazione delle visualizzazioni.

Per illustrare questo miglioramento, sono state generate due versioni del modello Navision usando EDMGen. *Nota: per una descrizione del modello Navision, vedere l'Appendice C.* Il modello di Navision è interessante per questo esercizio a causa della quantità molto elevata di entità e relazioni tra di esse.

Una versione di questo modello di grandi dimensioni è stata generata con le associazioni di chiavi esterne e l'altra è stata generata con associazioni indipendenti. È stato quindi programmato il tempo impiegato per generare le visualizzazioni per ogni modello. Entity Framework 5 test usava il Metodo GenerateViews () dalla classe EntityViewGenerator per generare le visualizzazioni, mentre il test Entity Framework 6 usava il Metodo GenerateViews () dalla classe StorageMappingItemCollection. A causa della ristrutturazione del codice che si è verificata nella codebase Entity Framework 6.

Utilizzando Entity Framework 5, la generazione di visualizzazioni per il modello con chiavi esterne richiede 65 minuti in un computer lab. È sconosciuto quanto tempo sarebbe stato necessario per generare le visualizzazioni per il modello che usava associazioni indipendenti. Il test è stato lasciato in esecuzione per più di un mese prima che il computer venisse riavviato nel Lab per installare gli aggiornamenti mensili.

Utilizzando Entity Framework 6, la generazione di visualizzazioni per il modello con chiavi esterne ha impiegato 28 secondi nello stesso computer lab. La generazione di visualizzazioni per il modello che usa associazioni indipendenti impiega 58 secondi. I miglioramenti apportati a Entity Framework 6 sul codice di generazione della visualizzazione significa che molti progetti non necessitano di visualizzazioni pre-generate per ottenere tempi di avvio più rapidi.

È importante ricordare che le visualizzazioni di pre-generazione in Entity Framework 4 e 5 possono essere eseguite con EDMGen o con Entity Framework Power Tools. Per Entity Framework 6 la generazione di visualizzazioni può essere eseguita tramite l'Entity Framework Power Tools o a livello di codice, come descritto in [viste di mapping pregenerate](xref:ef6/fundamentals/performance/pre-generated-views).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 come usare le chiavi esterne anziché le associazioni indipendenti

Quando si usa EDMGen o il Entity Designer in Visual Studio, si ottiene FKs per impostazione predefinita e si accetta solo una singola casella di controllo o flag della riga di comando per passare da FKs a IAs e viceversa.

Se si dispone di un modello di Code First di grandi dimensioni, l'utilizzo di associazioni indipendenti avrà lo stesso effetto sulla generazione della vista. È possibile evitare questo effetto includendo le proprietà di chiave esterna nelle classi per gli oggetti dipendenti, anche se alcuni sviluppatori considereranno questa operazione per inquinare il modello a oggetti. Altre informazioni su questo argomento sono disponibili in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/> .

| Quando si usa      | Procedere nel modo seguente                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Finestra di progettazione entità | Dopo aver aggiunto un'associazione tra due entità, assicurarsi di disporre di un vincolo referenziale. I vincoli referenziali indicano Entity Framework usare chiavi esterne anziché associazioni indipendenti. Per ulteriori informazioni \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework> , visitare. |
| EDMGen          | Quando si usa EDMGen per generare i file dal database, le chiavi esterne verranno rispettate e aggiunte al modello come tali. Per ulteriori informazioni sulle diverse opzioni esposte da EDMGen [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx) , visitare.                           |
| Code First      | Per informazioni su come includere proprietà di chiave esterna sugli oggetti dipendenti quando si usa Code First, vedere la sezione relativa alla Convenzione delle relazioni nell'argomento [convenzioni Code First](xref:ef6/modeling/code-first/conventions/built-in) .                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 passaggio del modello a un assembly separato

Quando il modello è incluso direttamente nel progetto dell'applicazione e le visualizzazioni vengono generate tramite un evento di pre-compilazione o un modello T4, la generazione e la convalida della visualizzazione verranno applicate ogni volta che il progetto viene ricompilato, anche se il modello non è stato modificato. Se si sposta il modello in un assembly separato e vi si fa riferimento dal progetto dell'applicazione, è possibile apportare altre modifiche all'applicazione senza dover ricompilare il progetto che contiene il modello.

*Nota:*   Quando si trasferisce il modello in assembly separati, ricordarsi di copiare le stringhe di connessione per il modello nel file di configurazione dell'applicazione del progetto client.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 disabilitare la convalida di un modello basato su edmx

I modelli EDMX vengono convalidati in fase di compilazione, anche se il modello è invariato. Se il modello è già stato convalidato, è possibile disattivare la convalida in fase di compilazione impostando la proprietà "convalida su compilazione" su false nella finestra Proprietà. Quando si modifica il mapping o il modello, è possibile riabilitare temporaneamente la convalida per verificare le modifiche.

Si noti che sono stati apportati miglioramenti alle prestazioni per la Entity Framework Designer per Entity Framework 6 e il costo della "convalida per compilazione" è molto più basso rispetto alle versioni precedenti della finestra di progettazione.

## <a name="3-caching-in-the-entity-framework"></a>3 Caching nella Entity Framework

Entity Framework presenta le seguenti forme di Caching predefinite:

1.  Caching degli oggetti: il ObjectStateManager incorporato in un'istanza di ObjectContext tiene traccia della memoria degli oggetti recuperati usando tale istanza. Questa operazione è nota anche come cache di primo livello.
2.  Caching del piano di query: riutilizzo del comando di archiviazione generato quando una query viene eseguita più volte.
3.  Caching dei metadati: condivisione dei metadati per un modello tra connessioni diverse allo stesso modello.

Oltre alle cache fornite da Entity Framework, è possibile usare un tipo speciale di provider di dati ADO.NET noto come provider di wrapping per estendere Entity Framework con una cache per i risultati recuperati dal database, nota anche come memorizzazione nella cache di secondo livello.

### <a name="31-object-caching"></a>3,1 Caching oggetti

Per impostazione predefinita, quando un'entità viene restituita nei risultati di una query, appena prima che EF lo materializza, ObjectContext verificherà se un'entità con la stessa chiave è già stata caricata nel relativo ObjectStateManager. Se un'entità con le stesse chiavi è già presente, EF li includerà nei risultati della query. Sebbene EF continui a eseguire la query sul database, questo comportamento può ignorare gran parte del costo di materializzazione dell'entità più volte.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 ottenere entità dalla cache oggetti usando DbContext Find

A differenza di una normale query, il metodo Find in DbSet (le API incluse per la prima volta in EF 4,1) eseguirà una ricerca in memoria prima di emettere anche la query sul database. È importante tenere presente che due diverse istanze di ObjectContext avranno due diverse istanze di ObjectStateManager, vale a dire che hanno cache di oggetti separate.

Trova usa il valore della chiave primaria per tentare di trovare un'entità rilevata dal contesto. Se l'entità non è nel contesto, una query viene eseguita e valutata rispetto al database e viene restituito null se l'entità non viene trovata nel contesto o nel database. Si noti che Find restituisce anche le entità che sono state aggiunte al contesto ma che non sono ancora state salvate nel database.

È necessario prendere in considerazione le prestazioni quando si usa find. Per impostazione predefinita, le chiamate a questo metodo attiverà una convalida della cache degli oggetti per rilevare le modifiche ancora in sospeso al database. Questo processo può essere molto dispendioso se è presente un numero molto elevato di oggetti nella cache degli oggetti o un oggetto grafico di grandi dimensioni aggiunto alla cache degli oggetti, ma può anche essere disabilitato. In alcuni casi, è possibile percepire un ordine di grandezza della differenza nella chiamata al metodo Find quando si disabilitano le modifiche del rilevamento automatico. Tuttavia, un secondo ordine di grandezza viene percepito quando l'oggetto è effettivamente nella cache rispetto al momento in cui l'oggetto deve essere recuperato dal database. Ecco un grafico di esempio con le misurazioni eseguite usando alcuni microbenchmark, espressi in millisecondi, con un carico di 5000 entità:

![Scala logaritmica .NET 4,5](~/ef6/media/net45logscale.png ".NET 4,5-scala logaritmica")

Esempio di ricerca con modifiche di rilevamento automatico disabilitate:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Quando si usa il metodo Find è necessario tenere presente quanto segue:

1.  Se l'oggetto non è nella cache, i vantaggi della ricerca sono negati, ma la sintassi è ancora più semplice rispetto a una query in base alla chiave.
2.  Se il rilevamento automatico delle modifiche è abilitato, il costo del metodo Find può aumentare di un ordine di grandezza o ancora più a seconda della complessità del modello e della quantità di entità nella cache degli oggetti.

Tenere inoltre presente che Find restituisce solo l'entità che si sta cercando e non carica automaticamente le entità associate se non sono già presenti nella cache degli oggetti. Se è necessario recuperare le entità associate, è possibile utilizzare una query in base alla chiave con caricamento eager. Per ulteriori informazioni, vedere **8,1 caricamento lazy rispetto al caricamento eager**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 problemi di prestazioni quando la cache degli oggetti include molte entità

La cache degli oggetti consente di aumentare la velocità di risposta complessiva del Entity Framework. Tuttavia, quando la cache degli oggetti ha una quantità molto elevata di entità caricate, può influire su determinate operazioni, ad esempio Aggiungi, Rimuovi, trova, immissione, SaveChanges e altro ancora. In particolare, le operazioni che attivano una chiamata a DetectChanges verranno influenzate negativamente dalle cache di oggetti di grandi dimensioni. DetectChanges sincronizza l'oggetto grafico con gestione stato oggetto e le relative prestazioni verranno determinate direttamente dalla dimensione dell'oggetto grafico. Per altre informazioni su DetectChanges, vedere [rilevamento delle modifiche nelle entità poco](https://msdn.microsoft.com/library/dd456848.aspx).

Quando si usa Entity Framework 6, gli sviluppatori sono in grado di chiamare AddRange e RemoveRange direttamente in un DbSet, anziché eseguire un'iterazione su una raccolta e chiamare Add una volta per ogni istanza. Il vantaggio di usare i metodi di intervallo è che il costo di DetectChanges viene pagato una sola volta per l'intero set di entità, anziché una volta per ogni entità aggiunta.

### <a name="32-query-plan-caching"></a>3,2 Caching del piano di query

La prima volta che viene eseguita una query, viene eseguito il compilatore del piano interno per tradurre la query concettuale nel comando Store, ad esempio, T-SQL che viene eseguito quando viene eseguito su SQL Server.Se la memorizzazione nella cache del piano di query è abilitata, la volta successiva che la query viene eseguita, il comando Store viene recuperato direttamente dalla cache dei piani di query per l'esecuzione, ignorando il compilatore del piano.

La cache dei piani di query è condivisa tra le istanze di ObjectContext nello stesso dominio AppDomain. Non è necessario utilizzare un'istanza di ObjectContext per trarre vantaggio dalla memorizzazione nella cache del piano di query.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 alcune note sulla memorizzazione nella cache del piano di query

-   La cache dei piani di query è condivisa per tutti i tipi di query: oggetti Entity SQL, LINQ to Entities e CompiledQuery.
-   Per impostazione predefinita, la memorizzazione nella cache dei piani di query è abilitata per Entity SQL query, eseguite tramite un oggetto EntityCommand o un oggetto ObjectQuery. È anche abilitata per impostazione predefinita per LINQ to Entities query in Entity Framework su .NET 4,5 e in Entity Framework 6
    -   La memorizzazione nella cache del piano di query può essere disabilitata impostando la proprietà EnablePlanCaching (su EntityCommand o ObjectQuery) su false. Ad esempio:
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   Per le query con parametri, la modifica del valore del parametro continuerà a raggiungere la query memorizzata nella cache. Tuttavia, la modifica di facet di un parametro (ad esempio, dimensioni, precisione o scala) raggiungerà una voce diversa nella cache.
-   Quando si usa Entity SQL, la stringa di query fa parte della chiave. La modifica della query comporterà voci di cache diverse, anche se le query sono equivalenti dal punto di vista funzionale. Sono incluse le modifiche apportate a maiuscole o minuscole.
-   Quando si usa LINQ, la query viene elaborata per generare una parte della chiave. La modifica dell'espressione LINQ genererà pertanto una chiave diversa.
-   Potrebbero essere applicate altre limitazioni tecniche; Per ulteriori informazioni, vedere query compilate in autocompilate.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 algoritmo di eliminazione della cache

La comprensione del funzionamento dell'algoritmo interno consentirà di capire quando abilitare o disabilitare la memorizzazione nella cache del piano di query. L'algoritmo di pulizia è il seguente:

1.  Quando la cache contiene un numero di voci impostato (800), viene avviato un timer che periodicamente (una volta al minuto) esegue lo sweep della cache.
2.  Durante le operazioni di sweep nella cache, le voci vengono rimosse dalla cache in una LFRU (meno frequente, usata di recente). Questo algoritmo prende in considerazione sia il numero di passaggi che l'età quando si decidono le voci da estrarre.
3.  Alla fine di ogni operazione di sweep della cache, la cache contiene nuovamente 800 voci.

Tutte le voci della cache vengono considerate ugualmente quando si determinano le voci da rimuovere. Questo significa che il comando Store per un CompiledQuery ha la stessa probabilità di sfratto del comando Store per una query Entity SQL.

Si noti che il timer di rimozione della cache viene avviato quando sono presenti 800 entità nella cache, ma la cache viene spazzata solo 60 secondi dopo l'avvio del timer. Ciò significa che per un massimo di 60 secondi, è possibile che la cache cresca in dimensioni molto elevate.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 metriche di test che dimostrano le prestazioni di memorizzazione nella cache del piano di query

Per illustrare l'effetto della memorizzazione nella cache del piano di query sulle prestazioni dell'applicazione, è stato eseguito un test in cui è stata eseguita una serie di Entity SQL query sul modello di Navision. Vedere l'appendice per una descrizione del modello Navision e i tipi di query eseguite. In questo test viene innanzitutto eseguita l'iterazione dell'elenco di query ed eseguita una volta per aggiungerle alla cache (se la memorizzazione nella cache è abilitata). Questo passaggio non è temporizzato. Successivamente, il thread principale verrà sospeso per più di 60 secondi per consentire l'esecuzione dello sweep della cache. Infine, si scorre l'elenco una seconda volta per eseguire le query memorizzate nella cache. Inoltre, la cache dei piani SQL Server viene scaricata prima dell'esecuzione di ogni set di query, in modo che i tempi ottenibili riflettano accuratamente il vantaggio fornito dalla cache dei piani di query.

##### <a name="3231-test-results"></a>Risultati test 3.2.3.1

| Test                                                                   | EF5 senza cache | EF5 memorizzato nella cache | EF6 senza cache | EF6 memorizzato nella cache |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Enumerazione di tutte le query 18723                                          | 124          | 125,4      | 124,3        | 125,3      |
| Evitare lo sweep (solo le prime 800 query, indipendentemente dalla complessità)  | 41,7         | 5.5        | 40.5         | 5.4        |
| Solo le query AggregatingSubtotals (178 Total, che evita lo sweep) | 39,5         | 4.5        | 38,1         | 4,6        |

*Tutti gli orari in secondi.*

Morale: durante l'esecuzione di un numero elevato di query distinte, ad esempio query create dinamicamente, la memorizzazione nella cache non è utile e il conseguente scaricamento della cache può garantire che le query che trarrebbero vantaggio dalla memorizzazione nella cache dei piani vengano effettivamente usate.

Le query AggregatingSubtotals sono le più complesse delle query sottoposte a test. Come previsto, più complessa è la query, maggiore sarà il vantaggio che verrà visualizzato dalla memorizzazione nella cache del piano di query.

Poiché un CompiledQuery è effettivamente una query LINQ con il piano memorizzato nella cache, il confronto tra un CompiledQuery e la query Entity SQL equivalente dovrebbe avere risultati simili. In realtà, se un'app dispone di molte query Entity SQL dinamiche, il riempimento della cache con le query comporterà anche la "decompilazione" di CompiledQueries quando vengono scaricati dalla cache. In questo scenario, le prestazioni possono essere migliorate disabilitando la memorizzazione nella cache per le query dinamiche per classificare in ordine di priorità i CompiledQueries. Meglio ancora, ovviamente, sarebbe riscrivere l'app in modo da usare query con parametri anziché query dinamiche.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3,3 uso di CompiledQuery per migliorare le prestazioni con le query LINQ

I test indicano che l'uso di CompiledQuery può offrire un vantaggio del 7% sulle query LINQ autocompilate. Ciò significa che il tempo di esecuzione del codice dallo stack di Entity Framework verrà speso del 7% meno. non significa che l'applicazione sarà più veloce del 7%. In generale, il costo per la scrittura e la gestione di oggetti CompiledQuery in EF 5,0 potrebbe non valere in caso di problemi rispetto ai vantaggi. Il chilometraggio può variare, quindi esercitare questa opzione se il progetto richiede la pressione aggiuntiva. Si noti che CompiledQueries sono compatibili solo con i modelli derivati da ObjectContext e non sono compatibili con i modelli derivati da DbContext.

Per altre informazioni sulla creazione e il richiamo di un CompiledQuery, vedere [query compilate (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Quando si usa un CompiledQuery, è necessario prendere in considerazione due considerazioni, ovvero la necessità di usare istanze statiche e i problemi che hanno con la composizione. Di seguito è riportata una spiegazione approfondita di queste due considerazioni.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 usare istanze CompiledQuery statiche

Poiché la compilazione di una query LINQ è un processo dispendioso in termini di tempo, non è necessario eseguire questa operazione ogni volta che è necessario recuperare i dati dal database. Le istanze di CompiledQuery consentono di compilare una sola volta ed eseguire più volte, ma è necessario prestare attenzione e procurarsi per riusare la stessa istanza di CompiledQuery ogni volta, anziché ricompilarla. L'uso di membri statici per archiviare le istanze di CompiledQuery diventa necessario; in caso contrario, non verrà visualizzato alcun vantaggio.

Si supponga, ad esempio, che la pagina disponga del corpo del metodo seguente per gestire la visualizzazione dei prodotti per la categoria selezionata:

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

In questo caso, si creerà una nuova istanza di CompiledQuery in tempo reale ogni volta che viene chiamato il metodo. Anziché visualizzare i vantaggi in termini di prestazioni recuperando il comando Store dalla cache dei piani di query, il CompiledQuery passerà attraverso il compilatore del piano ogni volta che viene creata una nuova istanza. In realtà, la cache dei piani di query verrà inquinata con una nuova voce CompiledQuery ogni volta che viene chiamato il metodo.

Si desidera invece creare un'istanza statica della query compilata, in modo da richiamare la stessa query compilata ogni volta che viene chiamato il metodo. A questo scopo, è possibile aggiungere l'istanza di CompiledQuery come membro del contesto dell'oggetto.È quindi possibile rendere le cose più pulite accedendo a CompiledQuery tramite un metodo helper:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

Questo metodo di supporto verrebbe richiamato come segue:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 composizione su un CompiledQuery

La possibilità di comporre una query LINQ è molto utile. a tale scopo, è sufficiente richiamare un metodo dopo l'oggetto IQueryable, ad esempio *Skip ()* o *Count ()*. Tuttavia, in questo modo essenzialmente restituisce un nuovo oggetto IQueryable. Anche se non c'è nulla da impedire tecnicamente di comporre in una CompiledQuery, questa operazione causerà la generazione di un nuovo oggetto IQueryable che richiede il passaggio di nuovo al compilatore del piano.

Alcuni componenti utilizzeranno oggetti IQueryable composti per abilitare funzionalità avanzate. Ad esempio, ASP. GridView di NET può essere associato a dati a un oggetto IQueryable tramite la proprietà SelectMethod. GridView comporrà quindi su questo oggetto IQueryable per consentire l'ordinamento e il paging sul modello di dati. Come si può notare, l'uso di un CompiledQuery per GridView non raggiunge la query compilata, ma genera una nuova query autocompilata.

Quando si aggiungono filtri progressivi a una query, è possibile che si verifichi questo problema. Si supponga, ad esempio, di avere una pagina Customers con diversi elenchi a discesa per i filtri facoltativi (ad esempio, Country e OrdersCount). È possibile comporre questi filtri sui risultati IQueryable di un CompiledQuery, ma questa operazione comporterà la nuova query per il compilatore del piano ogni volta che viene eseguita.

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Per evitare questa ricompilazione, è possibile riscrivere il CompiledQuery per tenere conto dei filtri possibili:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Che verrebbe richiamata nell'interfaccia utente, ad esempio:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Un compromesso è il fatto che il comando di archiviazione generato avrà sempre i filtri con i controlli null, ma questi dovrebbero essere abbastanza semplici da ottimizzare per il server di database:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3,4 caching dei metadati

Il Entity Framework supporta anche la memorizzazione dei metadati nella cache. Si tratta essenzialmente della memorizzazione nella cache delle informazioni sul tipo e delle informazioni sul mapping da tipo a database tra connessioni diverse allo stesso modello. La cache dei metadati è univoca per ogni dominio di applicazione.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 algoritmo di caching dei metadati

1.  Le informazioni sui metadati per un modello sono archiviate in un elemento ItemCollection per ogni EntityConnection.
    -   Si noti che esistono diversi oggetti ItemCollection per diverse parti del modello. Ad esempio, StoreItemCollections contiene le informazioni sul modello di database. ObjectItemCollection contiene informazioni sul modello di dati. EdmItemCollection contiene informazioni sul modello concettuale.

2.  Se due connessioni utilizzano la stessa stringa di connessione, condividono la stessa istanza di ItemCollection.
3.  Equivalenti dal punto di vista funzionale, ma in modo testuale diverse stringhe di connessione possono produrre cache di metadati diverse. Le stringhe di connessione di tokenize vengono eseguite, quindi la semplice modifica dell'ordine dei token dovrebbe generare metadati condivisi. Tuttavia, due stringhe di connessione che sembrano funzionalmente uguali non possono essere valutate come identiche dopo la suddivisione in token.
4.  ItemCollection viene verificato periodicamente per l'utilizzo. Se è stato determinato che l'accesso a un'area di lavoro non è stato eseguito di recente, verrà contrassegnato per la pulizia nella successiva operazione di memorizzazione nella cache.
5.  La semplice creazione di un oggetto EntityConnection provocherà la creazione di una cache di metadati (sebbene le raccolte di elementi in essa non verranno inizializzate fino all'apertura della connessione). Questa area di lavoro rimarrà in memoria fino a quando l'algoritmo di memorizzazione nella cache non ne determina la mancata "utilizzo".

Il team di consulenza clienti ha scritto un post di Blog che descrive la conservazione di un riferimento a un elemento ItemCollection per evitare la "deprecazione" quando si usano modelli di grandi dimensioni: \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services> .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 la relazione tra Caching dei metadati e caching del piano di query

L'istanza della cache del piano di query risiede nell'elemento ItemCollection dei tipi di archivio di MetadataWorkspace. Ciò significa che i comandi dell'archivio memorizzati nella cache verranno utilizzati per le query su qualsiasi contesto di cui è stata creata un'istanza utilizzando un elemento MetadataWorkspace specificato. Indica inoltre che se si dispone di due stringhe di connessione leggermente diverse e non corrispondenti dopo suddivisione in token, saranno disponibili diverse istanze della cache del piano di query.

### <a name="35-results-caching"></a>3,5 memorizzazione dei risultati nella cache

Con la memorizzazione dei risultati nella cache (nota anche come "memorizzazione nella cache di secondo livello"), si conservano i risultati delle query in una cache locale. Quando si emette una query, si verifica prima di tutto se i risultati sono disponibili localmente prima di eseguire una query sull'archivio. Sebbene la memorizzazione nella cache dei risultati non sia supportata direttamente da Entity Framework, è possibile aggiungere una cache di secondo livello utilizzando un provider di wrapping. Un esempio di provider di wrapping con una cache di secondo livello è Alachisoft [Entity Framework cache di secondo livello basata su NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Questa implementazione della memorizzazione nella cache di secondo livello è una funzionalità inserita che si verifica dopo che l'espressione LINQ è stata valutata (e funcletized) e il piano di esecuzione della query viene calcolato o recuperato dalla cache di primo livello. La cache di secondo livello archivia quindi solo i risultati del database non elaborato, quindi la pipeline di materializzazione viene comunque eseguita in seguito.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 ulteriori riferimenti per la memorizzazione nella cache dei risultati con il provider di wrapping

-   Julie Lerman ha scritto un "caching di secondo livello in Entity Framework e Windows Azure" articolo di MSDN che include come aggiornare il provider di wrapping di esempio per l'uso della memorizzazione nella cache di Windows Server AppFabric: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Se si lavora con Entity Framework 5, nel Blog del team è presente un post che descrive come eseguire le operazioni con il provider di caching per Entity Framework 5: \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider> . Include anche un modello T4 che consente di automatizzare l'aggiunta della memorizzazione nella cache di secondo livello al progetto.

## <a name="4-autocompiled-queries"></a>4 query compilate in Autocompilazione

Quando una query viene eseguita su un database usando Entity Framework, deve eseguire una serie di passaggi prima di materializzazione effettivamente i risultati; uno di questi passaggi è la compilazione di query. Entity SQL le query erano note a prestazioni ottimali, in quanto vengono automaticamente memorizzate nella cache, quindi la seconda o la terza volta che si esegue la stessa query può ignorare il compilatore del piano e utilizzare il piano memorizzato nella cache.

In Entity Framework 5 è stata introdotta la memorizzazione automatica nella cache anche per LINQ to Entities query. Nelle versioni precedenti di Entity Framework la creazione di un CompiledQuery per velocizzare le prestazioni era una pratica comune, perché ciò renderebbe la LINQ to Entities query memorizzabile nella cache. Poiché la memorizzazione nella cache viene ora eseguita automaticamente senza l'uso di un CompiledQuery, questa funzionalità viene chiamata "query autocompilate". Per ulteriori informazioni sulla cache dei piani di query e sui relativi meccanismi, vedere la pagina relativa alla memorizzazione nella cache del piano di query.

Entity Framework rileva quando una query richiede la ricompilazione ed esegue questa operazione quando la query viene richiamata anche se era stata compilata in precedenza. Le condizioni comuni che provocano la ricompilazione della query sono le seguenti:

-   Modifica dell'oggetto MergeOption associato alla query. La query memorizzata nella cache non verrà utilizzata, ma il compilatore del piano verrà eseguito nuovamente e il piano appena creato verrà memorizzato nella cache.
-   Modifica del valore di ContextOptions. UseCSharpNullComparisonBehavior. Si ottiene lo stesso effetto della modifica di MergeOption.

Altre condizioni possono impedire alla query di usare la cache. Esempi comuni:

-   Utilizzo di IEnumerable &lt; T &gt; . Contains &lt; &gt; (valore T).
-   Utilizzo di funzioni che producono query con costanti.
-   Uso delle proprietà di un oggetto non mappato.
-   Collegamento della query a un'altra query che richiede la ricompilazione.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4,1 uso di IEnumerable &lt; T &gt; . Contains &lt; t &gt; (valore t)

Entity Framework non memorizza nella cache le query che richiamano IEnumerable &lt; T &gt; . Contiene &lt; t &gt; (t value) rispetto a una raccolta in memoria, poiché i valori della raccolta sono considerati volatili. La query di esempio seguente non verrà memorizzata nella cache, quindi verrà sempre elaborata dal compilatore del piano:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

Si noti che la dimensione dell'oggetto IEnumerable rispetto al quale viene eseguito il metodo determina la velocità o la modalità di compilazione della query. Le prestazioni possono soffrire in modo significativo quando si usano raccolte di grandi dimensioni, ad esempio quella illustrata nell'esempio precedente.

Entity Framework 6 contiene le ottimizzazioni per il modo in cui IEnumerable &lt; T &gt; . Contains &lt; t &gt; (t value) funziona quando vengono eseguite le query. Il codice SQL generato è molto più veloce da produrre e più leggibile e, nella maggior parte dei casi, viene eseguito più velocemente nel server.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4,2 uso di funzioni che producono query con costanti

Gli operatori LINQ (), Take (), Contains () e DefautIfEmpty () LINQ non producono query SQL con parametri, ma inseriscono invece i valori passati come costanti. Per questo motivo, le query che potrebbero altrimenti risultare identiche a causa dell'inquinamento della cache dei piani di query, sia nello stack EF che nel server di database, non vengono riutilizzate a meno che non si utilizzino le stesse costanti in un'esecuzione di query successiva. Ad esempio:

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

In questo esempio, ogni volta che la query viene eseguita con un valore diverso per l'ID, la query verrà compilata in un nuovo piano.

In particolare, prestare attenzione all'uso di Skip e Take durante il paging. In EF6 questi metodi hanno un overload lambda che rende il piano di query memorizzato nella cache riutilizzabile perché EF è in grado di acquisire variabili passate a questi metodi e di convertirle in SqlParameters. Questo consente anche di proteggere la cache, poiché in caso contrario ogni query con una costante diversa per Skip e Take otterrebbe la voce della cache del piano di query.

Si consideri il codice seguente, che non è ottimale ma che è destinato solo a esemplificare questa classe di query:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Una versione più veloce dello stesso codice comporterebbe la chiamata a Skip con un'espressione lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Il secondo frammento di codice può essere eseguito fino all'11% più velocemente perché lo stesso piano di query viene usato ogni volta che viene eseguita la query, il che consente di risparmiare tempo CPU ed evitare l'inquinamento della cache delle query. Inoltre, poiché il parametro da ignorare si trova in una chiusura, il codice potrebbe essere simile al seguente:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4,3 utilizzo delle proprietà di un oggetto non mappato

Quando una query usa le proprietà di un tipo di oggetto non mappato come parametro, la query non viene memorizzata nella cache. Ad esempio:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

In questo esempio si supponga che la classe NonMappedType non faccia parte del modello di entità. È possibile modificare facilmente questa query in modo da non utilizzare un tipo non mappato e utilizzare invece una variabile locale come parametro per la query:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

In questo caso, la query sarà in grado di essere memorizzata nella cache e trarrà vantaggio dalla cache dei piani di query.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4,4 collegamento a query che richiedono la ricompilazione

Seguendo lo stesso esempio precedente, se si dispone di una seconda query che si basa su una query che deve essere ricompilata, verrà ricompilata anche l'intera seconda query. Di seguito è riportato un esempio per illustrare questo scenario:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

L'esempio è generico, ma illustra il modo in cui il collegamento a firstQuery causa la mancata memorizzazione nella cache di secondQuery. Se firstQuery non era una query che richiede la ricompilazione, secondQuery sarebbe stato memorizzato nella cache.

## <a name="5-notracking-queries"></a>5 NoTracking di query

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5,1 disabilitazione del rilevamento delle modifiche per ridurre il sovraccarico della gestione dello stato

Se si è in uno scenario di sola lettura e si desidera evitare il sovraccarico dovuto al caricamento degli oggetti in ObjectStateManager, è possibile eseguire query di "nessun rilevamento".Il rilevamento delle modifiche può essere disabilitato a livello di query.

Si noti tuttavia che disabilitando il rilevamento delle modifiche si disattiva effettivamente la cache degli oggetti. Quando si esegue una query per un'entità, non è possibile ignorare la materializzazione estraendo i risultati della query precedentemente materializzata da ObjectStateManager. Se si eseguono ripetutamente query per le stesse entità nello stesso contesto, è possibile che si possa ottenere un vantaggio in merito alle prestazioni dall'abilitazione del rilevamento delle modifiche.

Quando si eseguono query utilizzando ObjectContext, le istanze di ObjectQuery e ObjectSet ricorderanno un oggetto MergeOption dopo che è stato impostato e le query che sono composte su di essi erediteranno l'oggetto MergeOption effettivo della query padre. Quando si usa DbContext, è possibile disabilitare il rilevamento chiamando il modificatore AsNoTracking () in DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 disabilitazione del rilevamento delle modifiche per una query quando si usa DbContext

È possibile impostare la modalità di una query su NoTracking concatenando una chiamata al metodo AsNoTracking () nella query. Diversamente da ObjectQuery, le classi DbSet e DbQuery nell'API DbContext non hanno una proprietà modificabile per MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Disabilitazione del rilevamento delle modifiche a livello di query tramite ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Disabilitazione del rilevamento delle modifiche per un intero set di entità mediante ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5,2 metriche di test che dimostrano il vantaggio in merito alle prestazioni delle query NoTracking

In questo test viene esaminato il costo del riempimento del ObjectStateManager confrontando il rilevamento per le query NoTracking per il modello Navision. Vedere l'appendice per una descrizione del modello Navision e i tipi di query eseguite. In questo test viene eseguito l'iterazione nell'elenco di query ed eseguita una sola volta. Abbiamo eseguito due varianti del test, una volta con le query di NoTracking e una volta con l'opzione di unione predefinita "AppendOnly". Ogni variazione è stata eseguita 3 volte e si accetta il valore medio delle esecuzioni. Tra i test si cancella la cache delle query nel SQL Server e si compatta il tempdb eseguendo i comandi seguenti:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Risultati test, mediana su 3 esecuzioni:

|                        | NESSUN RILEVAMENTO-WORKING SET | NESSUN RILEVAMENTO – TEMPO | SOLO ACCODAMENTO-WORKING SET | SOLO ACCODAMENTO-ORA |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 MS         | 596545536                 | 1273042 MS         |
| **Entity Framework 6** | 647127040                 | 190228 MS          | 832798720                 | 195521 MS          |

Entity Framework 5 avrà un footprint di memoria inferiore alla fine dell'esecuzione rispetto a Entity Framework 6. La memoria aggiuntiva utilizzata da Entity Framework 6 è il risultato di strutture e codice di memoria aggiuntive che consentono nuove funzionalità e prestazioni migliori.

Quando si usa ObjectStateManager, c'è anche una differenza netta nel footprint di memoria. Entity Framework 5 ha aumentato il footprint del 30% quando tiene traccia di tutte le entità materializzate dal database. Quando si esegue questa operazione, Entity Framework 6 ha aumentato il footprint del 28%.

In termini di tempo, Entity Framework 6 supera Entity Framework 5 in questo test da un margine elevato. Entity Framework 6 ha completato il test in circa il 16% del tempo utilizzato da Entity Framework 5. Inoltre, Entity Framework 5 impiega il 10% più tempo per il completamento quando viene utilizzato ObjectStateManager. In confronto, Entity Framework 6 sta utilizzando il 3% più tempo quando si utilizza ObjectStateManager.

## <a name="6-query-execution-options"></a>6 opzioni di esecuzione delle query

Entity Framework offre diversi modi per eseguire una query. Si esamineranno le opzioni seguenti, si confronteranno i vantaggi e gli svantaggi di ciascuno di essi e si esamineranno le relative caratteristiche di prestazioni:

-   LINQ to Entities.
-   Nessun LINQ to Entities di rilevamento.
-   Entity SQL su un oggetto ObjectQuery.
-   Entity SQL su un oggetto EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6,1 query LINQ to Entities

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Vantaggi**

-   Adatto per le operazioni CUD.
-   Oggetti completamente materializzati.
-   Più semplice da scrivere con la sintassi incorporata nel linguaggio di programmazione.
-   Prestazioni ottimali.

**Svantaggi**

-   Alcune restrizioni tecniche, ad esempio:
    -   I modelli che utilizzano DefaultIfEmpty per le query OUTER JOIN generano query più complesse rispetto alle semplici istruzioni OUTER JOIN in Entity SQL.
    -   Non è ancora possibile utilizzare LIKE con criteri di ricerca generali.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6,2 nessuna query LINQ to Entities di rilevamento

Quando il contesto deriva ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Quando il contesto deriva DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Vantaggi**

-   Miglioramento delle prestazioni rispetto alle normali query LINQ.
-   Oggetti completamente materializzati.
-   Più semplice da scrivere con la sintassi incorporata nel linguaggio di programmazione.

**Svantaggi**

-   Non adatto per le operazioni CUD.
-   Alcune restrizioni tecniche, ad esempio:
    -   I modelli che utilizzano DefaultIfEmpty per le query OUTER JOIN generano query più complesse rispetto alle semplici istruzioni OUTER JOIN in Entity SQL.
    -   Non è ancora possibile utilizzare LIKE con criteri di ricerca generali.

Si noti che le query che proiettano le proprietà scalari non vengono rilevate anche se il NoTracking non è specificato. Ad esempio:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Questa specifica query non specifica in modo esplicito l'oggetto NoTracking, ma poiché non materializzazione un tipo noto al gestore dello stato dell'oggetto, il risultato materializzato non viene rilevato.

### <a name="63-entity-sql-over-an-objectquery"></a>6,3 Entity SQL su un oggetto ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Vantaggi**

-   Adatto per le operazioni CUD.
-   Oggetti completamente materializzati.
-   Supporta la memorizzazione nella cache del piano di query.

**Svantaggi**

-   Include stringhe di query testuali più soggette a errori dell'utente rispetto ai costrutti di query incorporati nel linguaggio.

### <a name="64-entity-sql-over-an-entity-command"></a>6,4 Entity SQL su un comando di entità

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**Vantaggi**

-   Supporta la memorizzazione nella cache del piano di query in .NET 4,0 (la memorizzazione nella cache dei piani è supportata da tutti gli altri tipi di query in .NET 4,5).

**Svantaggi**

-   Include stringhe di query testuali più soggette a errori dell'utente rispetto ai costrutti di query incorporati nel linguaggio.
-   Non adatto per le operazioni CUD.
-   I risultati non vengono materializzati automaticamente e devono essere letti dal lettore dati.

### <a name="65-sqlquery-and-executestorequery"></a>6,5 SQLQuery e ExecuteStoreQuery

SqlQuery sul database:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery su DbSet:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Vantaggi**

-   Prestazioni in genere più veloci perché il compilatore del piano viene ignorato.
-   Oggetti completamente materializzati.
-   Adatto per le operazioni CUD quando viene usato da DbSet.

**Svantaggi**

-   La query è testuale e soggetta a errori.
-   La query è associata a un back-end specifico usando la semantica dell'archivio invece della semantica concettuale.
-   Quando è presente l'ereditarietà, la query Handcrafted deve tenere conto delle condizioni di mapping per il tipo richiesto.

### <a name="66-compiledquery"></a>6,6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Vantaggi**

-   Fornisce un miglioramento delle prestazioni del 7% rispetto alle normali query LINQ.
-   Oggetti completamente materializzati.
-   Adatto per le operazioni CUD.

**Svantaggi**

-   Maggiore complessità e sovraccarico di programmazione.
-   Il miglioramento delle prestazioni viene perso quando si compone una query compilata.
-   Alcune query LINQ non possono essere scritte come CompiledQuery, ad esempio proiezioni di tipi anonimi.

### <a name="67-performance-comparison-of-different-query-options"></a>6,7 confronto delle prestazioni di diverse opzioni di query

I microbenchmark semplici in cui la creazione del contesto non era temporizzata sono stati inseriti nel test. È stata misurata una query di 5000 volte per un set di entità non memorizzate nella cache in un ambiente controllato. Questi numeri devono essere creati con un avviso: non riflettono i numeri effettivi prodotti da un'applicazione, ma sono una misurazione molto accurata della differenza tra le prestazioni in caso di confronto tra mele e mele, escluso il costo della creazione di un nuovo contesto.

| EF  | Test                                 | Tempo (MS) | Memoria   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ESQL ObjectContext                   | 2414      | 38801408 |
| EF5 | Query LINQ ObjectContext             | 2692      | 38277120 |
| EF5 | DbContext query LINQ senza rilevamento     | 2818      | 41840640 |
| EF5 | Query LINQ DbContext                 | 2930      | 41771008 |
| EF5 | Query LINQ ObjectContext senza rilevamento | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ESQL ObjectContext                   | 2059      | 46039040 |
| EF6 | Query LINQ ObjectContext             | 3074      | 45248512 |
| EF6 | DbContext query LINQ senza rilevamento     | 3125      | 47575040 |
| EF6 | Query LINQ DbContext                 | 3420      | 47652864 |
| EF6 | Query LINQ ObjectContext senza rilevamento | 3593      | 45260800 |

![EF5 micro benchmarks, 5000 iterazioni calde](~/ef6/media/ef5micro5000warm.png)

![EF6 micro benchmarks, 5000 iterazioni calde](~/ef6/media/ef6micro5000warm.png)

I microbenchmark sono molto sensibili alle piccole modifiche del codice. In questo caso, la differenza tra i costi di Entity Framework 5 e Entity Framework 6 è causata dall'aggiunta dei miglioramenti di [intercettazione](xref:ef6/fundamentals/logging-and-interception) e [transazionali](xref:ef6/saving/transactions). Questi numeri di microbenchmark, tuttavia, sono una visione amplificata in un frammento molto piccolo di ciò che Entity Framework. Gli scenari reali di query calde non dovrebbero vedere una regressione delle prestazioni durante l'aggiornamento da Entity Framework 5 a Entity Framework 6.

Per confrontare le prestazioni reali delle diverse opzioni di query, sono state create 5 diverse varianti di test in cui viene usata un'opzione di query diversa per selezionare tutti i prodotti il cui nome di categoria è "Beverage". Ogni iterazione include il costo della creazione del contesto e il costo di materializzazione di tutte le entità restituite. 10 iterazioni vengono eseguite prima di accettare la somma di 1000 iterazioni temporizzate. I risultati visualizzati sono la mediana eseguita da 5 esecuzioni di ogni test. Per ulteriori informazioni, vedere l'Appendice B, che include il codice per il test.

| EF  | Test                                        | Tempo (MS) | Memoria   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Comando entità ObjectContext                | 621       | 39350272 |
| EF5 | DbContext query SQL sul database             | 825       | 37519360 |
| EF5 | Query archivio ObjectContext                   | 878       | 39460864 |
| EF5 | Query LINQ ObjectContext senza rilevamento        | 969       | 38293504 |
| EF5 | Entity SQL di ObjectContext con query di oggetto | 1089      | 38981632 |
| EF5 | Query compilata ObjectContext                | 1099      | 38682624 |
| EF5 | Query LINQ ObjectContext                    | 1152      | 38178816 |
| EF5 | DbContext query LINQ senza rilevamento            | 1208      | 41803776 |
| EF5 | Query SQL di DbContext su DbSet                | 1414      | 37982208 |
| EF5 | Query LINQ DbContext                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Comando entità ObjectContext                | 480       | 47247360 |
| EF6 | Query archivio ObjectContext                   | 493       | 46739456 |
| EF6 | DbContext query SQL sul database             | 614       | 41607168 |
| EF6 | Query LINQ ObjectContext senza rilevamento        | 684       | 46333952 |
| EF6 | Entity SQL di ObjectContext con query di oggetto | 767       | 48865280 |
| EF6 | Query compilata ObjectContext                | 788       | 48467968 |
| EF6 | DbContext query LINQ senza rilevamento            | 878       | 47554560 |
| EF6 | Query LINQ ObjectContext                    | 953       | 47632384 |
| EF6 | Query SQL di DbContext su DbSet                | 1023      | 41992192 |
| EF6 | Query LINQ DbContext                        | 1290      | 47529984 |


![Iterazioni di EF5 warm query 1000](~/ef6/media/ef5warmquery1000.png)

![Iterazioni di EF6 warm query 1000](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Per completezza, è stata inclusa una variante in cui viene eseguita una query di Entity SQL su un oggetto EntityCommand. Tuttavia, poiché i risultati non vengono materializzati per tali query, il confronto non è necessariamente da mele a mele. Il test include un'approssimazione vicina a materializzazione per provare a rendere più equo il confronto.

In questo caso end-to-end, Entity Framework 6 supera Entity Framework 5 grazie ai miglioramenti apportati alle prestazioni in diverse parti dello stack, tra cui un'inizializzazione DbContext più leggera e una ricerca più veloce di MetaDataCollection &lt; T &gt; .

## <a name="7-design-time-performance-considerations"></a>7 considerazioni sulle prestazioni in fase di progettazione

### <a name="71-inheritance-strategies"></a>7,1 strategie di ereditarietà

Un altro aspetto della valutazione delle prestazioni quando si usa Entity Framework è la strategia di ereditarietà usata. Entity Framework supporta 3 tipi di ereditarietà di base e le relative combinazioni:

-   Tabella per gerarchia (TPH): ogni set di ereditarietà viene mappato a una tabella con una colonna discriminatore per indicare quale tipo specifico della gerarchia viene rappresentato nella riga.
-   Tabella per tipo (TPT): ogni tipo ha una propria tabella nel database. le tabelle figlio definiscono solo le colonne che la tabella padre non contiene.
-   Tabella per classe (TPC), in cui ogni tipo ha una propria tabella completa nel database. le tabelle figlio definiscono tutti i campi, inclusi quelli definiti nei tipi padre.

Se il modello utilizza l'ereditarietà TPT, le query generate saranno più complesse rispetto a quelle generate con le altre strategie di ereditarietà, che potrebbero causare tempi di esecuzione più lunghi nell'archivio.In genere, la generazione di query su un modello TPT richiede più tempo e per materializzare gli oggetti risultanti.

Vedere "Considerazioni sulle prestazioni quando si usa l'ereditarietà TPT (tabella per tipo) nel Entity Framework" post di Blog MSDN: \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework> .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 evitare TPT in applicazioni Model First o Code First

Quando si crea un modello su un database esistente con uno schema TPT, non sono disponibili molte opzioni. Tuttavia, quando si crea un'applicazione usando Model First o Code First, è consigliabile evitare l'ereditarietà TPT per i problemi relativi alle prestazioni.

Quando si usa Model First nella procedura guidata Entity Designer, si otterranno TPT per qualsiasi ereditarietà nel modello. Se si vuole passare a una strategia di ereditarietà TPH con Model First, è possibile usare "Entity Designer database Generation Power Pack" disponibile in Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/> ).

Quando si utilizza Code First per configurare il mapping di un modello con ereditarietà, EF utilizzerà TPH per impostazione predefinita, pertanto tutte le entità nella gerarchia di ereditarietà verranno mappate alla stessa tabella. Per altri dettagli, vedere la sezione "mapping con l'API Fluent" dell'articolo "Code First in Entity Framework 4.1" in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx) ).

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7,2 aggiornamento da EF4 per migliorare l'ora di generazione del modello

Un miglioramento specifico di SQL Server dell'algoritmo che genera il livello di archiviazione (SSDL) del modello è disponibile in Entity Framework 5 e 6 e come aggiornamento a Entity Framework 4 quando viene installato Visual Studio 2010 SP1. I risultati dei test seguenti illustrano il miglioramento quando si genera un modello molto grande, in questo caso il modello di Navision. Per altri dettagli, vedere l'Appendice C.

Il modello contiene set di entità 1005 e 4227 set di associazioni.

| Configurazione                              | Suddivisione del tempo utilizzato                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | Generazione SSDL: 2 ore 27 min <br/> Generazione mapping: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione visualizzazione: 2 h 14 min |
| Visual Studio 2010 SP1, Entity Framework 4 | Generazione SSDL: 1 secondo <br/> Generazione mapping: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione visualizzazione: 1 hr 53 min   |
| Visual Studio 2013, Entity Framework 5     | Generazione SSDL: 1 secondo <br/> Generazione mapping: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione visualizzazione: 65 minuti    |
| Visual Studio 2013, Entity Framework 6     | Generazione SSDL: 1 secondo <br/> Generazione mapping: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione visualizzazione: 28 secondi.   |


Vale la pena notare che, durante la generazione del linguaggio SSDL, il carico è quasi completamente dedicato alla SQL Server, mentre il computer di sviluppo client è in attesa di inattività affinché i risultati tornino dal server. DBA dovrebbe apprezzare in particolare questo miglioramento. È anche importante notare che essenzialmente l'intero costo della generazione del modello avviene ora nella generazione di viste.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7,3 Suddivisione di modelli di grandi dimensioni con Database First e Model First

Con l'aumentare delle dimensioni del modello, l'area di progettazione diventa disordinata e difficile da utilizzare. In genere, un modello con più di 300 entità è troppo grande per poter utilizzare in modo efficace la finestra di progettazione. Il post di Blog seguente descrive diverse opzioni per suddividere i modelli di grandi dimensioni: \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2> .

Il post è stato scritto per la prima versione di Entity Framework, ma la procedura è ancora applicabile.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7,4 Considerazioni sulle prestazioni con il controllo origine dati entità

Sono stati rilevati casi di test di stress e prestazioni multithread in cui le prestazioni di un'applicazione Web che usano il controllo EntityDataSource si deteriorano significativamente. La ragione principale è che EntityDataSource chiama ripetutamente MetadataWorkspace. LoadFromAssembly sugli assembly a cui fa riferimento l'applicazione Web per individuare i tipi da utilizzare come entità.

La soluzione consiste nell'impostare ContextTypeName di EntityDataSource sul nome del tipo della classe ObjectContext derivata. Questo disattiva il meccanismo che analizza tutti gli assembly a cui si fa riferimento per i tipi di entità.

L'impostazione del campo ContextTypeName impedisce anche un problema funzionale in cui EntityDataSource in .NET 4,0 genera un ReflectionTypeLoadException quando non è in grado di caricare un tipo da un assembly tramite reflection. Questo problema è stato risolto in .NET 4,5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7,5 entità POCO e proxy rilevamento modifiche

Entity Framework consente di utilizzare le classi di dati personalizzate insieme al modello di dati senza apportare alcuna modifica alle classi di dati stesse. Pertanto è possibile pertanto utilizzare oggetti POCO (Plain-Old CLR Object), ad esempio gli oggetti di dominio esistenti, con il modello di dati. Queste classi di dati POCO, note anche come oggetti che non riconoscono la persistenza, di cui è stato eseguito il mapping alle entità definite in un modello di dati, supportano la maggior parte degli stessi comportamenti di query, inserimento, aggiornamento ed eliminazione dei tipi di entità generati dagli strumenti Entity Data Model.

Entity Framework inoltre possibile creare classi proxy derivate dai tipi POCO, che vengono utilizzate quando si desidera abilitare funzionalità quali il caricamento lazy e il rilevamento automatico delle modifiche nelle entità POCO. Le classi POCO devono soddisfare determinati requisiti per consentire Entity Framework di usare i proxy, come descritto qui: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx) .

I proxy di rilevamento delle probabilità notificheranno al gestore dello stato dell'oggetto ogni volta che viene modificato il valore di una delle proprietà delle entità, quindi Entity Framework sa sempre lo stato effettivo delle entità. Questa operazione viene eseguita tramite l'aggiunta di eventi di notifica al corpo dei metodi setter delle proprietà e la gestione dello stato dell'oggetto che elabora tali eventi. Si noti che la creazione di un'entità proxy sarà in genere più costosa della creazione di un'entità POCO proxy non proxy a causa del set aggiunto di eventi creati da Entity Framework.

Quando un'entità POCO non dispone di un proxy di rilevamento delle modifiche, le modifiche vengono rilevate confrontando il contenuto delle entità con una copia di uno stato salvato in precedenza. Questo confronto approfondito diventerà un processo lungo quando si dispone di molte entità nel contesto o quando le entità hanno una quantità molto elevata di proprietà, anche se nessuna di esse è stata modificata dopo l'ultimo confronto.

In breve, durante la creazione del proxy di rilevamento delle modifiche si pagherà un riscontro delle prestazioni, ma il rilevamento delle modifiche consente di velocizzare il processo di rilevamento delle modifiche quando le entità hanno molte proprietà o quando sono presenti molte entità nel modello. Per le entità con un numero ridotto di proprietà in cui la quantità di entità non aumenta troppo, i proxy di rilevamento delle modifiche potrebbero non essere di gran lunga vantaggio.

## <a name="8-loading-related-entities"></a>8 caricamento di entità correlate

### <a name="81-lazy-loading-vs-eager-loading"></a>8,1 caricamento lazy rispetto al caricamento eager

Entity Framework offre diversi modi per caricare le entità correlate all'entità di destinazione. Ad esempio, quando si esegue una query per i prodotti, esistono diversi modi in cui gli ordini correlati verranno caricati nel gestore dello stato dell'oggetto. Dal punto di vista delle prestazioni, la domanda più importante da considerare quando si caricano entità correlate sarà se usare il caricamento lazy o il caricamento eager.

Quando si usa il caricamento eager, le entità correlate vengono caricate insieme al set di entità di destinazione. Usare un'istruzione include nella query per indicare quali entità correlate si desidera includere.

Quando si usa il caricamento lazy, la query iniziale porta solo il set di entità di destinazione. Tuttavia, ogni volta che si accede a una proprietà di navigazione, viene eseguita un'altra query sull'archivio per caricare l'entità correlata.

Dopo che un'entità è stata caricata, eventuali altre query per l'entità verranno caricate direttamente dal gestore dello stato dell'oggetto, indipendentemente dal fatto che si stia usando il caricamento lazy o il caricamento eager.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8,2 come scegliere tra il caricamento lazy e il caricamento eager

L'aspetto importante è che si comprende la differenza tra il caricamento lazy e il caricamento eager, in modo che sia possibile effettuare la scelta corretta per l'applicazione. Ciò consentirà di valutare il compromesso tra più richieste nel database rispetto a una singola richiesta che può contenere un payload di grandi dimensioni. Potrebbe essere opportuno usare il caricamento eager in alcune parti dell'applicazione e il caricamento lazy in altre parti.

Come esempio di ciò che accade dietro le quinte, si supponga di voler eseguire una query per i clienti che risiedono nel Regno Unito e il conteggio degli ordini.

**Uso del caricamento eager**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Uso del caricamento lazy**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

Quando si usa il caricamento eager, viene eseguita una singola query che restituisce tutti i clienti e tutti gli ordini. Il comando Store è simile al seguente:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

Quando si usa il caricamento lazy, è necessario eseguire inizialmente la query seguente:

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

Ogni volta che si accede alla proprietà di navigazione Orders di un cliente, viene eseguita un'altra query simile alla seguente nell'archivio:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

Per ulteriori informazioni, vedere [caricamento di oggetti correlati](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>foglio informativo caricamento lazy 8.2.1 rispetto al caricamento eager

Non c'è niente di simile a una delle dimensioni adatte per scegliere il caricamento eager rispetto al caricamento lazy. Provare prima di tutto a comprendere le differenze tra le due strategie, in modo da poter prendere una decisione ben formata; valutare inoltre se il codice si riferisce a uno degli scenari seguenti:

| Scenario                                                                    | Il nostro suggerimento                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| È necessario accedere a molte proprietà di navigazione dalle entità recuperate? | **No** -entrambe le opzioni probabilmente funzioneranno. Tuttavia, se il payload che la query sta portando non è troppo grande, è possibile che si verifichino vantaggi in termini di prestazioni tramite il caricamento eager, perché sarà necessario meno round trip in rete per materializzare gli oggetti. <br/> <br/> **Sì** : se è necessario accedere a molte proprietà di navigazione dalle entità, è possibile usare più istruzioni include nella query con caricamento eager. Maggiore è il numero di entità incluse, più grande sarà il payload restituito dalla query. Una volta incluse nella query tre o più entità, provare a passare al caricamento lazy. |
| Si conoscono esattamente quali dati saranno necessari in fase di esecuzione?                   | **No** Il caricamento lazy sarà migliore. In caso contrario, è possibile che si verifichino query per i dati che non sono necessari. <br/> <br/> **Sì** : il caricamento eager è probabilmente la scommessa migliore. consente di caricare i set interi più velocemente. Se la query richiede il recupero di una quantità molto elevata di dati e questo diventa troppo lento, provare a eseguire il caricamento lazy.                                                                                                                                                                                                                                                       |
| Il codice è in esecuzione lontano dal database? (aumento della latenza di rete)  | **No** : quando la latenza di rete non è un problema, l'uso del caricamento lazy può semplificare il codice. Tenere presente che la topologia dell'applicazione può cambiare, quindi non considerare la prossimità del database per l'autorizzazione. <br/> <br/> **Sì** : quando la rete è un problema, solo è possibile decidere quale sia la soluzione migliore per il proprio scenario. In genere, il caricamento eager sarà migliore perché richiede meno round trip.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>problemi di prestazioni 8.2.2 con più inclusioni

Quando si ricevono domande sulle prestazioni che coinvolgono problemi del tempo di risposta del server, l'origine del problema è spesso query con più istruzioni di inclusione. Sebbene l'inclusione di entità correlate in una query sia efficace, è importante capire cosa accade dietro le quinte.

Il tempo impiegato da una query con più istruzioni include per la creazione del comando di archiviazione richiede un tempo relativamente lungo. La maggior parte di questo tempo è dedicata al tentativo di ottimizzare la query risultante. Il comando archivio generato conterrà un outer join o un'Unione per ogni inclusione, a seconda del mapping. Le query di questo tipo porteranno i grafici di grandi dimensioni dal database in un unico payload, che acerbate i problemi di larghezza di banda, soprattutto quando si verifica una notevole ridondanza nel payload (ad esempio, quando vengono usati più livelli di inclusione per attraversare le associazioni nella direzione uno-a-molti).

È possibile controllare i casi in cui le query restituiscono payload eccessivamente grandi accedendo al TSQL sottostante per la query usando ToTraceString ed eseguendo il comando Store in SQL Server Management Studio per visualizzare le dimensioni del payload. In questi casi, è possibile provare a ridurre il numero di istruzioni include nella query per importare solo i dati necessari. In alternativa, potrebbe essere possibile suddividere la query in una sequenza più piccola di sottoquery, ad esempio:

**Prima di suddividere la query:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**Al termine della query,**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

Questa operazione funzionerà solo per le query rilevate, poiché viene usata la capacità del contesto di eseguire automaticamente la risoluzione delle identità e la correzione dell'associazione.

Come per il caricamento lazy, il compromesso sarà costituito da un numero maggiore di query per payload più piccoli. È inoltre possibile utilizzare le proiezioni delle singole proprietà per selezionare in modo esplicito solo i dati necessari da ogni entità, ma in questo caso le entità non verranno caricate e gli aggiornamenti non saranno supportati.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>soluzione alternativa 8.2.3 per ottenere il caricamento lazy delle proprietà

Entity Framework attualmente non supporta il caricamento lazy di proprietà scalari o complesse. Tuttavia, nei casi in cui si dispone di una tabella che include un oggetto di grandi dimensioni, ad esempio un BLOB, è possibile utilizzare la suddivisione delle tabelle per separare le proprietà di grandi dimensioni in un'entità separata. Si supponga, ad esempio, di disporre di una tabella Product che includa una colonna della foto varbinary. Se non è spesso necessario accedere a questa proprietà nelle query, è possibile utilizzare la suddivisione delle tabelle per importare solo le parti dell'entità normalmente necessarie. L'entità che rappresenta la foto del prodotto verrà caricata solo quando è necessaria in modo esplicito.

Una risorsa efficace che illustra come abilitare la suddivisione delle tabelle è il post di Blog relativo alla suddivisione delle tabelle nel Entity Framework di Gil Fink: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx> .

## <a name="9-other-considerations"></a>altre 9 considerazioni

### <a name="91-server-garbage-collection"></a>Garbage Collection per Server 9,1

Alcuni utenti potrebbero riscontrare conflitti di risorse che limitano il parallelismo previsto quando il Garbage Collector non è configurato correttamente. Quando EF viene usato in uno scenario a thread multipli o in qualsiasi applicazione simile a un sistema lato server, assicurarsi di abilitare la Garbage Collection per server. Questa operazione viene eseguita tramite un'impostazione semplice nel file di configurazione dell'applicazione:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Questa operazione dovrebbe ridurre la contesa dei thread e aumentare la velocità effettiva fino al 30% negli scenari saturi della CPU. In termini generali, è sempre necessario testare il comportamento dell'applicazione usando la procedura di Garbage Collection classica (ottimizzata per gli scenari di interfaccia utente e lato client), nonché l'operazione di Garbage Collection per server.

### <a name="92-autodetectchanges"></a>9,2 AutoDetectChanges

Come indicato in precedenza, Entity Framework potrebbe visualizzare problemi di prestazioni quando la cache degli oggetti include molte entità. Alcune operazioni, ad esempio Add, Remove, find, entry e SaveChanges, attivano chiamate a DetectChanges che potrebbero utilizzare una grande quantità di CPU in base alla dimensione della cache degli oggetti. Il motivo è che la cache degli oggetti e il gestore dello stato dell'oggetto provano a rimanere sincronizzati il più possibile in ogni operazione eseguita in un contesto, in modo che i dati prodotti siano corretti in un'ampia gamma di scenari.

È in genere consigliabile lasciare Entity Framework il rilevamento delle modifiche automatico abilitato per l'intera durata dell'applicazione. Se lo scenario è influenzato da un utilizzo elevato della CPU e i profili indicano che il problema è la chiamata a DetectChanges, è consigliabile disattivare temporaneamente AutoDetectChanges nella parte sensibile del codice:

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

Prima di disattivare AutoDetectChanges, è opportuno comprendere che questo potrebbe causare la perdita della capacità di Entity Framework di rilevare determinate informazioni sulle modifiche apportate alle entità. Se gestita in modo non corretto, è possibile che si verifichi un'incoerenza dei dati nell'applicazione. Per ulteriori informazioni sulla disattivazione di AutoDetectChanges, vedere \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/> .

### <a name="93-context-per-request"></a>9,3 contesto per richiesta

I contesti di Entity Framework devono essere usati come istanze di breve durata per offrire l'esperienza ottimale per le prestazioni. I contesti dovrebbero essere di breve durata ed eliminati e, di conseguenza, sono stati implementati in modo da essere molto leggeri e riutilizzarli laddove possibile. Negli scenari Web è importante tenerlo presente e non avere un contesto per più della durata di una singola richiesta. Analogamente, in scenari non Web, il contesto deve essere scartato in base alla conoscenza dei diversi livelli di memorizzazione nella cache nel Entity Framework. In generale, è consigliabile evitare di avere un'istanza del contesto per tutta la durata dell'applicazione, nonché i contesti per thread e contesti statici.

### <a name="94-database-null-semantics"></a>semantica null del database 9,4

Entity Framework per impostazione predefinita genererà codice SQL con \# semantica di confronto null C. Si consideri la query di esempio seguente:

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

In questo esempio vengono confrontate alcune variabili nullable per le proprietà Nullable sull'entità, ad esempio SupplierID e PrezzoUnitario. L'oggetto SQL generato per questa query richiederà se il valore del parametro è uguale al valore della colonna o se il parametro e i valori della colonna sono null. Questo consente di nascondere il modo in cui il server di database gestisce i valori null e offrirà un'esperienza coerente con i \# valori null tra diversi fornitori di database. D'altra parte, il codice generato è un po' contorto e potrebbe non funzionare correttamente quando la quantità di confronti nell'istruzione WHERE della query diventa un numero elevato.

Un modo per gestire questa situazione consiste nell'usare la semantica null del database. Si noti che questo comportamento potrebbe potenzialmente comportarsi in modo diverso \# rispetto alla semantica null C poiché ora Entity Framework genererà SQL più semplice che espone il modo in cui il motore di database gestisce i valori null. La semantica null del database può essere attivata per contesto con una singola riga di configurazione rispetto alla configurazione del contesto:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Le query di dimensioni medio-piccole non visualizzeranno un miglioramento delle prestazioni percettibile quando si usa la semantica null del database, ma la differenza diventerà più evidente nelle query con un numero elevato di potenziali confronti NULL.

Nella query di esempio precedente la differenza delle prestazioni era minore del 2% in un microbenchmark eseguito in un ambiente controllato.

### <a name="95-async"></a>9,5 asincrono

In Entity Framework 6 è stato introdotto il supporto di operazioni asincrone durante l'esecuzione in .NET 4,5 o versioni successive. Nella maggior parte dei casi, le applicazioni che hanno una contesa correlata a IO trarranno maggiori vantaggi dall'uso di operazioni di salvataggio e query asincrone. Se l'applicazione non è soggetta a contesa di i/o, l'uso di Async, nei casi migliori, viene eseguito in modo sincrono e restituisce il risultato nella stessa quantità di tempo di una chiamata sincrona o nel peggiore dei casi, è sufficiente rinviare l'esecuzione a un'attività asincrona e aggiungere ulteriore tempo al completamento dello scenario.

Per informazioni sul funzionamento della programmazione asincrona che consentirà di decidere se Async migliorerà le prestazioni della visita dell'applicazione [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx) . Per altre informazioni sull'uso di operazioni asincrone in Entity Framework, vedere [query asincrona e Salva](xref:ef6/fundamentals/async
).

### <a name="96-ngen"></a>9,6 NGEN

Entity Framework 6 non viene eseguita l'installazione predefinita di .NET Framework. Di conseguenza, gli assembly di Entity Framework non sono di tipo NGEN per impostazione predefinita, pertanto tutto il codice di Entity Framework è soggetto agli stessi costi di JIT'ing di qualsiasi altro assembly MSIL. Questo potrebbe influire negativamente sull'esperienza F5 durante lo sviluppo e anche l'avvio a freddo dell'applicazione negli ambienti di produzione. Per ridurre i costi di utilizzo della CPU e della memoria di JIT'ing, è consigliabile fare in modo che NGEN le immagini Entity Framework nel modo appropriato. Per altre informazioni su come migliorare le prestazioni di avvio di Entity Framework 6 con NGEN, vedere [miglioramento delle prestazioni di avvio con NGen](xref:ef6/fundamentals/performance/ngen).

### <a name="97-code-first-versus-edmx"></a>9,7 Code First rispetto a EDMX

Entity Framework motivi del problema di mancata corrispondenza dell'impedenza tra la programmazione orientata a oggetti e i database relazionali tramite una rappresentazione in memoria del modello concettuale (gli oggetti), lo schema di archiviazione (il database) e un mapping tra i due. Questi metadati sono detti Entity Data Model o EDM per brevità. Da questo modello EDM, Entity Framework deriverà le visualizzazioni ai dati di andata e ritorno dagli oggetti in memoria al database e viceversa.

Quando Entity Framework viene usato con un file EDMX che specifica formalmente il modello concettuale, lo schema di archiviazione e il mapping, la fase di caricamento del modello deve solo convalidare la correttezza dell'EDM (ad esempio, assicurarsi che non siano presenti mapping), quindi generare le visualizzazioni, quindi convalidare le viste e preparare i metadati per l'uso. Solo in questo modo è possibile eseguire una query o salvare nuovi dati nell'archivio dati.

L'approccio Code First è, al suo cuore, un generatore di Entity Data Model sofisticato. Il Entity Framework deve produrre un modello EDM dal codice fornito; Questa operazione viene eseguita analizzando le classi incluse nel modello, applicando le convenzioni e configurando il modello tramite l'API Fluent. Dopo la compilazione del modello EDM, il Entity Framework si comporta in modo analogo al fatto che un file EDMX era presente nel progetto. Pertanto, la compilazione del modello da Code First aggiunge una complessità aggiuntiva che si traduce in un tempo di avvio più lento per l'Entity Framework rispetto alla presenza di un EDMX. Il costo dipende completamente dalla dimensione e dalla complessità del modello in fase di compilazione.

Quando si sceglie di usare EDMX e Code First, è importante tenere presente che la flessibilità introdotta da Code First aumenta il costo della compilazione del modello per la prima volta. Se l'applicazione è in grado di sostenere il costo del primo carico, in genere Code First sarà il modo migliore per iniziare.

## <a name="10-investigating-performance"></a>10 analisi delle prestazioni

### <a name="101-using-the-visual-studio-profiler"></a>10,1 uso del profiler di Visual Studio

Se si verificano problemi di prestazioni con il Entity Framework, è possibile usare un profiler come quello incorporato in Visual Studio per vedere dove l'applicazione spende il proprio tempo. Questo è lo strumento usato per generare i grafici a torta nel post di Blog "esplorazione delle prestazioni del ADO.NET Entity Framework-parte 1" ( \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1> ) che mostra dove Entity Framework trascorre il tempo durante le query a freddo e a caldo.

Il post di Blog relativo all'Entity Framework di profilatura con il profiler di Visual Studio 2010 Profiler scritto dal team di consulenza clienti sui dati e sulla modellazione Mostra un esempio reale del modo in cui hanno usato il profiler per esaminare un problema di   \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler> prestazioni. Questo post è stato scritto per un'applicazione Windows. Se è necessario profilare un'applicazione Web, è possibile che gli strumenti Windows Performance Recorder (WPR) e Windows Performance Analyzer (WPA) funzionino meglio rispetto a Visual Studio. WPR e WPA fanno parte del Windows Performance Toolkit, incluso in Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982) ).

### <a name="102-applicationdatabase-profiling"></a>10,2 profilatura di applicazioni/database

Strumenti come il profiler incorporato in Visual Studio indicano i punti in cui l'applicazione dedica tempo.È disponibile un altro tipo di profiler che esegue l'analisi dinamica dell'applicazione in esecuzione, in produzione o pre-produzione a seconda delle esigenze, e cerca problemi comuni e anti-modelli di accesso al database.

Due profiler disponibili in commercio sono i Entity Framework Profiler ( \<http://efprof.com> ) e ORMProfiler ( \<http://ormprofiler.com> ).

Se l'applicazione è un'applicazione MVC che usa Code First, è possibile usare MiniProfiler di StackExchange. Scott Hanselr descrive questo strumento nel suo Blog all'indirizzo: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx> .

Per altre informazioni sulla profilatura dell'attività del database dell'applicazione, vedere l'articolo di MSDN Magazine di Julie Lerman intitolato [attività di database di profilatura nella Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>logger di database 10,3

Se si usa Entity Framework 6, considerare anche l'uso della funzionalità di registrazione incorporata. È possibile indicare alla proprietà database del contesto di registrare l'attività tramite una semplice configurazione a una riga:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

In questo esempio l'attività del database verrà registrata nella console, ma la proprietà log può essere configurata in modo da chiamare qualsiasi delegato della stringa di azione &lt; &gt; .

Se si vuole abilitare la registrazione del database senza ricompilare e si usa Entity Framework 6,1 o versione successiva, è possibile aggiungere un intercettore nel file web.config o app.config dell'applicazione.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Per ulteriori informazioni su come aggiungere la registrazione senza ricompilare Vai a \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/> .

## <a name="11-appendix"></a>11 Appendice

### <a name="111-a-test-environment"></a>11,1 A. ambiente di test

Questo ambiente usa un'installazione a 2 computer con il database in un computer separato dall'applicazione client. I computer si trovano nello stesso rack, quindi la latenza di rete è relativamente bassa, ma più realistica di un ambiente con un singolo computer.

#### <a name="1111-app-server"></a>Server App 11.1.1

##### <a name="11111-software-environment"></a>Ambiente software 11.1.1.1

-   Ambiente software Entity Framework 4
    -   Nome del sistema operativo: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (solo per alcuni confronti).
-   Ambiente software Entity Framework 5 e 6
    -   Nome del sistema operativo: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>Ambiente hardware 11.1.1.2

-   Processore doppio: Intel (R) Xeon (R) CPU L5520 W3530 @ 2.27 GHz, 2261 Mhz8 GHz, 4 core, 84 processori logici.
-   2412 GB RamRAM.
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s unità suddivisa in 4 partizioni.

#### <a name="1112-db-server"></a>server di database 11.1.2

##### <a name="11121-software-environment"></a>Ambiente software 11.1.2.1

-   Nome del sistema operativo: Windows Server 2008 R 28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>Ambiente hardware 11.1.2.2

-   Processore singolo: Intel (R) Xeon (R) CPU L5520 @ 2.27 GHz, 2261 MHz-1620 0 @ 3.60 GHz, 4 core, 8 processori logici.
-   824 GB RamRAM.
-   465 GB ATA500GB unità SATA 7200 rpm 6GB/s divise in 4 partizioni.

### <a name="112-b-query-performance-comparison-tests"></a>11,2 B. test di confronto delle prestazioni delle query

Il modello Northwind è stato utilizzato per eseguire questi test. È stata generata dal database usando la finestra di progettazione Entity Framework. Quindi, per confrontare le prestazioni delle opzioni di esecuzione delle query è stato usato il codice seguente:

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>11,3 modello C. Navision

Il database di Navision è un database di grandi dimensioni usato per la demo di Microsoft Dynamics-NAV. Il modello concettuale generato contiene set di entità 1005 e 4227 set di associazioni. Il modello utilizzato nel test è "flat", a cui non è stata aggiunta alcuna ereditarietà.

#### <a name="1131-queries-used-for-navision-tests"></a>query 11.3.1 usate per i test di Navision

L'elenco di query usato con il modello Navision contiene 3 categorie di query di Entity SQL:

##### <a name="11311-lookup"></a>ricerca 11.3.1.1

Query di ricerca semplice senza aggregazioni

-   Conteggio: 16232
-   Esempio:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Una query BI normale con più aggregazioni, ma senza subtotali (query singola)

-   Conteggio: 2313
-   Esempio:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Dove \_ il valore \_ di SessionLogin tempo \_ di MDF massimo () è definito nel modello come:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

Query BI con aggregazioni e subtotali (tramite Union All)

-   Conteggio: 178
-   Esempio:

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
