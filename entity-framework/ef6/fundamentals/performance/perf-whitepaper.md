---
title: Considerazioni sulle prestazioni per EF4, EF5 ed EF6 - EF6Performance considerations for EF4, EF5, and EF6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434339"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Considerazioni sulle prestazioni per EF 4, 5 e 6Performance considerations for EF 4, 5, and 6
Di David Obando, Eric Dettinger e altri

Pubblicato: aprile 2012

Ultimo aggiornamento: maggio 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introduzione

I framework di mapping relazionale a oggetti sono un modo pratico per fornire un'astrazione per l'accesso ai dati in un'applicazione orientata agli oggetti. Per le applicazioni .NET, L'O/RM consigliato da Microsoft è Entity Framework. Con qualsiasi astrazione, tuttavia, le prestazioni possono diventare un problema.

Questo white paper è stato scritto per mostrare le considerazioni sulle prestazioni durante lo sviluppo di applicazioni che utilizzano Entity Framework, per fornire agli sviluppatori un'idea degli algoritmi interni di Entity Framework che possono influire sulle prestazioni e per fornire suggerimenti per l'analisi e il miglioramento delle prestazioni nelle applicazioni che utilizzano Entity Framework. Ci sono una serie di buoni argomenti sulle prestazioni già disponibili sul web, e abbiamo anche provato a puntare a queste risorse dove possibile.

Le prestazioni sono un argomento difficile. Questo white paper è inteso come una risorsa che consente di prendere decisioni relative alle prestazioni per le applicazioni che utilizzano Entity Framework. Sono state incluse alcune metriche di test per illustrare le prestazioni, ma queste metriche non sono intese come indicatori assoluti delle prestazioni che verranno visualizzate nell'applicazione.

Per scopi pratici, in questo documento si presuppone che Entity Framework 4 venga eseguito in .NET 4.0 e che Entity Framework 5 e 6 vengano eseguiti in .NET 4.5. Molti dei miglioramenti delle prestazioni apportati per Entity Framework 5 risiedono all'interno dei componenti di base che vengono riprodotti con .NET 4.5.

Entity Framework 6 è una versione fuori banda e non dipende dai componenti di Entity Framework che vengono spediti con .NET. Entity Framework 6 funziona sia su .NET 4.0 che su .NET 4.5 e può offrire un grande vantaggio in termini di prestazioni a coloro che non hanno eseguito l'aggiornamento da .NET 4.0 ma desiderano i bit di Entity Framework più recenti nell'applicazione. Quando questo documento menziona Entity Framework 6, si riferisce alla versione più recente disponibile al momento della stesura di questo articolo: versione 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. Esecuzione di query a freddo e a caldo

La prima volta che viene eseguita una query su un determinato modello, Entity Framework esegue molte operazioni dietro le quinte per caricare e convalidare il modello. Si fa spesso riferimento a questa prima query come a una query "fredda".Ulteriori query su un modello già caricato sono note come query "calde" e sono molto più veloci.

Prendiamo una vista generale del punto in cui viene impiegato il tempo durante l'esecuzione di una query con Entity Framework e vediamo dove le cose stanno migliorando in Entity Framework 6.

**Prima esecuzione query - query a freddo**

| Scrittura utente codice                                                                                     | Azione                    | Impatto sulle prestazioni di EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Impatto sulle prestazioni di EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Impatto sulle prestazioni di EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creazione di contesto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                        | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creazione di espressioni di query | Basso                                                                                                                                                                                                                                                                                                                                                                                                                           | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Esecuzione di query LINQ      | - Caricamento dei metadati: alto ma memorizzato nella cache- Metadata loading: High but cached <br/> - Generazione della vista: potenzialmente molto alta ma memorizzata nella cache- View generation: Potentially very high but cached <br/> - Valutazione dei parametri: Media <br/> - Traduzione di query: Medio <br/> - Generazione di materializer: media ma memorizzata nella cache- Materialzer generation: Medium but cached <br/> - Esecuzione di query di database: potenzialmente elevata- Database query execution: Potentially high <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media <br/> - Ricerca identità: Media | - Caricamento dei metadati: alto ma memorizzato nella cache- Metadata loading: High but cached <br/> - Generazione della vista: potenzialmente molto alta ma memorizzata nella cache- View generation: Potentially very high but cached <br/> - Valutazione dei parametri: Bassa <br/> - Traduzione di query: media ma memorizzata nella cache- Query translation: Medium but cached <br/> - Generazione di materializer: media ma memorizzata nella cache- Materialzer generation: Medium but cached <br/> - Esecuzione di query di database: potenzialmente elevato (query migliori in alcune situazioni)- Database query execution: Potentially high (Better queries in some situations) <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media <br/> - Ricerca identità: Media | - Caricamento dei metadati: alto ma memorizzato nella cache- Metadata loading: High but cached <br/> - Generazione della vista: Media ma memorizzata nella cache- View generation: Medium but cached <br/> - Valutazione dei parametri: Bassa <br/> - Traduzione di query: media ma memorizzata nella cache- Query translation: Medium but cached <br/> - Generazione di materializer: media ma memorizzata nella cache- Materialzer generation: Medium but cached <br/> - Esecuzione di query di database: potenzialmente elevato (query migliori in alcune situazioni)- Database query execution: Potentially high (Better queries in some situations) <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media (più veloce di EF5) <br/> - Ricerca identità: Media |
| `}`                                                                                                  | Connection.Chiudi          | Basso                                                                                                                                                                                                                                                                                                                                                                                                                           | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Seconda esecuzione query - query a caldo**

| Scrittura utente codice                                                                                     | Azione                    | Impatto sulle prestazioni di EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Impatto sulle prestazioni di EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Impatto sulle prestazioni di EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creazione di contesto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creazione di espressioni di query | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Esecuzione di query LINQ      | - Ricerca di caricamento dei metadati: ~~alta ma memorizzata nella cache~~ Bassa- Metadata ~~loading~~ lookup: High but cached Low <br/> - Ricerca ~~generazione~~ vista: ~~potenzialmente molto alta ma memorizzata nella cache~~ Bassa <br/> - Valutazione dei parametri: Media <br/> - Ricerca ~~traduzione~~ query: Media <br/> - Ricerca ~~generation~~ generazione materializzatore: ~~media ma memorizzata nella cache~~ Bassa <br/> - Esecuzione di query di database: potenzialmente elevata- Database query execution: Potentially high <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media <br/> - Ricerca identità: Media | - Ricerca di caricamento dei metadati: ~~alta ma memorizzata nella cache~~ Bassa- Metadata ~~loading~~ lookup: High but cached Low <br/> - Ricerca ~~generazione~~ vista: ~~potenzialmente molto alta ma memorizzata nella cache~~ Bassa <br/> - Valutazione dei parametri: Bassa <br/> - Ricerca ~~di traduzione~~ di query: ~~Media ma memorizzata nella cache~~ Bassa <br/> - Ricerca ~~generation~~ generazione materializzatore: ~~media ma memorizzata nella cache~~ Bassa <br/> - Esecuzione di query di database: potenzialmente elevato (query migliori in alcune situazioni)- Database query execution: Potentially high (Better queries in some situations) <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media <br/> - Ricerca identità: Media | - Ricerca di caricamento dei metadati: ~~alta ma memorizzata nella cache~~ Bassa- Metadata ~~loading~~ lookup: High but cached Low <br/> - Ricerca ~~generazione~~ vista: ~~Media ma memorizzata nella cache~~ Bassa <br/> - Valutazione dei parametri: Bassa <br/> - Ricerca ~~di traduzione~~ di query: ~~Media ma memorizzata nella cache~~ Bassa <br/> - Ricerca ~~generation~~ generazione materializzatore: ~~media ma memorizzata nella cache~~ Bassa <br/> - Esecuzione di query di database: potenzialmente elevato (query migliori in alcune situazioni)- Database query execution: Potentially high (Better queries in some situations) <br/> Connessione.Apri <br/> Comando.EsecuzioneReader <br/> DataReader.Read <br/> Materializzazione dell'oggetto: Media (più veloce di EF5) <br/> - Ricerca identità: Media |
| `}`                                                                                                  | Connection.Chiudi          | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Basso                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Esistono diversi modi per ridurre il costo delle prestazioni delle query a freddo e a caldo e verranno visualizzati nella sezione seguente. In particolare, si esaminerà la riduzione del costo del caricamento del modello nelle query a freddo utilizzando viste pregenerate, che dovrebbe contribuire ad alleviare i problemi di prestazioni riscontrati durante la generazione della visualizzazione. Per le query a caldo, verranno illustrate la memorizzazione nella cache del piano di query, nessuna query di rilevamento e diverse opzioni di esecuzione delle query.

### <a name="21-what-is-view-generation"></a>2.1 Che cos'è la generazione di viste?

Per comprendere che cos'è la generazione di viste, dobbiamo prima capire cosa sono le "Visualizzazioni di mapping". Le visualizzazioni di mapping sono rappresentazioni eseguibili delle trasformazioni specificate nel mapping per ogni set di entità e associazione. Internamente, queste visualizzazioni di mapping assumono la forma di CQT (alberi di query canonici). Esistono due tipi di viste di mapping:

-   Viste query: rappresentano la trasformazione necessaria per passare dallo schema del database al modello concettuale.
-   Visualizzazioni di aggiornamento: rappresentano la trasformazione necessaria per passare dal modello concettuale allo schema del database.

Tenere presente che il modello concettuale potrebbe differire dallo schema del database in vari modi. Ad esempio, una singola tabella può essere utilizzata per archiviare i dati per due tipi di entità diversi. Ereditarietà e mapping non banali svolgono un ruolo nella complessità delle visualizzazioni di mapping.

Il processo di calcolo di queste visualizzazioni in base alla specifica del mapping è ciò che chiamiamo generazione di visualizzazioni. La generazione della vista può avvenire in modo dinamico quando un modello viene caricato, o in fase di compilazione, utilizzando "viste pregenerate"; questi ultimi vengono serializzati sotto forma di istruzioni\# Entity SQL in un file C o VB.

Quando le visualizzazioni vengono generate, vengono convalidate anche. Dal punto di vista delle prestazioni, la maggior parte del costo della generazione della vista è in realtà la convalida delle viste che assicura che le connessioni tra le entità abbiano senso e abbiano la cardinalità corretta per tutte le operazioni supportate.

Quando viene eseguita una query su un set di entità, la query viene combinata con la visualizzazione di query corrispondente e il risultato di questa composizione viene eseguito tramite il compilatore di piani per creare la rappresentazione della query che l'archivio di backup può comprendere. Per SQL Server, il risultato finale di questa compilazione sarà un'istruzione T-SQL SELECT. La prima volta che viene eseguito un aggiornamento su un set di entità, la visualizzazione di aggiornamento viene eseguita tramite un processo simile per trasformarla in istruzioni DML per il database di destinazione.

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 Fattori che influiscono sulle prestazioni di generazione della vista

Le prestazioni del passaggio di generazione della vista dipendono non solo dalle dimensioni del modello, ma anche da quanto è interconnesso il modello. Se due entità sono connesse tramite una catena di ereditarietà o un'associazione, si dice che siano connesse. Analogamente, se due tabelle sono connesse tramite una chiave esterna, sono connesse. Con l'aumento del numero di entità e tabelle connesse negli schemi, il costo di generazione della vista aumenta.

L'algoritmo che usiamo per generare e convalidare le viste è esponenziale nel peggiore dei casi, anche se utilizziamo alcune ottimizzazioni per migliorare questo. I fattori più importanti che sembrano influire negativamente sulle prestazioni sono:

-   Dimensioni del modello, che fanno riferimento al numero di entità e alla quantità di associazioni tra queste entità.
-   Complessità del modello, in particolare l'ereditarietà che coinvolge un numero elevato di tipi.
-   Utilizzo di associazioni indipendenti anziché di associazioni di chiavi esterne.

Per i modelli piccoli e semplici il costo può essere sufficientemente piccolo da non preoccuparsi di utilizzare viste pregenerate. Con l'aumento delle dimensioni e della complessità del modello, sono disponibili diverse opzioni per ridurre il costo della generazione e della convalida delle viste.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 Utilizzo di viste pregenerate per ridurre il tempo di caricamento del modello

Per informazioni dettagliate su come utilizzare le visualizzazioni pregenerate in Entity Framework 6, visitare le visualizzazioni di [mapping pregenerate](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 Visualizzazioni pregenerate utilizzando Entity Framework Power Tools Community Edition

È possibile utilizzare [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) per generare visualizzazioni dei modelli EDMX e Code First facendo clic con il pulsante destro del mouse sul file di classe del modello e utilizzando il menu Entity Framework per selezionare "Genera viste". L'edizione della community di Entity Framework Power Tools funziona solo nei contesti derivati da DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Come utilizzare le viste pregenerate con un modello creato da EDMGen

EDMGen è un'utilità che viene fornita con .NET e funziona con Entity Framework 4 e 5, ma non con Entity Framework 6. EDMGen consente di generare un file di modello, il livello oggetto e le viste dalla riga di comando. Uno degli output sarà un file Views nel linguaggio\#di scelta, VB o C . Si tratta di un file di codice contenente frammenti di Entity SQLEntity SQL per ogni set di entità. Per abilitare le visualizzazioni pregenerate, è sufficiente includere il file nel progetto.

Se si apportano manualmente modifiche ai file di schema per il modello, sarà necessario generare nuovamente il file delle viste. A tale scopo, eseguire EDMGen con il flag **/mode:ViewGeneration.**

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Come utilizzare le viste pre-generate con un file EDMX

È inoltre possibile utilizzare EDMGen per generare visualizzazioni per un file EDMX - l'argomento MSDN a cui si fa riferimento in precedenza viene descritto come aggiungere un evento di pre-compilazione per eseguire questa operazione - ma questo è complicato e ci sono alcuni casi in cui non è possibile. In genere è più semplice usare un modello T4 per generare le viste quando il modello si trova in un file edmx.

Il blog del team di ADO.NET contiene un post che descrive \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)come utilizzare un modello T4 per la generazione di visualizzazioni ( . Questo post include un modello che può essere scaricato e aggiunto al progetto. Il modello è stato scritto per la prima versione di Entity Framework, pertanto non è garantito che funzioni con le versioni più recenti di Entity Framework. Tuttavia, è possibile scaricare un set più aggiornato di modelli di generazione della visualizzazione per Entity Framework 4 e 5 da Visual Studio Gallery:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Se si utilizza Entity Framework 6 è possibile ottenere i modelli T4 di generazione della visualizzazione da Visual Studio Gallery all'indirizzo \< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 Riduzione del costo di generazione della vista

L'utilizzo di viste pregenerate sposta il costo della generazione della vista dal caricamento del modello (tempo di esecuzione) alla fase di progettazione. Anche se questo migliora le prestazioni di avvio in fase di esecuzione, si verificherà ancora il dolore della generazione di visualizzazione durante lo sviluppo. Esistono diversi trucchi aggiuntivi che consentono di ridurre il costo della generazione della visualizzazione, sia in fase di compilazione che in fase di esecuzione.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 Utilizzo di associazioni di chiavi esterne per ridurre i costi di generazione della vista

Abbiamo visto un certo numero di casi in cui il passaggio delle associazioni nel modello da associazioni indipendenti ad associazioni di chiavi esterne ha migliorato notevolmente il tempo trascorso nella generazione della vista.

Per dimostrare questo miglioramento, abbiamo generato due versioni del modello Navision utilizzando EDMGen. *Nota: vedere l'appendice C per una descrizione del modello Navision.* Il modello Navision è interessante per questo esercizio a causa della sua grande quantità di entità e delle relazioni tra di loro.

Una versione di questo modello molto grande è stata generata con le associazioni di chiavi esterne e l'altra è stata generata con associazioni indipendenti. Abbiamo quindi periodo il tempo necessario per generare le viste per ogni modello. Il test di Entity Framework 5 ha utilizzato il metodo GenerateViews() della classe EntityViewGenerator per generare le visualizzazioni, mentre il test di Entity Framework 6 ha utilizzato il metodo GenerateViews() dalla classe StorageMappingItemCollection. Ciò è dovuto alla ristrutturazione del codice che si è verificato nella codebase di Entity Framework 6.

Utilizzando Entity Framework 5, la generazione di visualizzazioni per il modello con chiavi esterne ha richiesto 65 minuti in un computer lab. Non è noto quanto tempo ci sarebbe voluto per generare le viste per il modello che utilizzava associazioni indipendenti. Abbiamo lasciato il test in esecuzione per oltre un mese prima che la macchina è stato riavviato nel nostro laboratorio per installare gli aggiornamenti mensili.

Utilizzando Entity Framework 6, la generazione di visualizzazioni per il modello con chiavi esterne ha richiesto 28 secondi nello stesso computer lab. La generazione della vista per il modello che utilizza associazioni indipendenti ha richiesto 58 secondi. I miglioramenti apportati a Entity Framework 6 nel codice di generazione della visualizzazione significano che molti progetti non avranno bisogno di visualizzazioni pregenerate per ottenere tempi di avvio più rapidi.

È importante sottolineare che la pre-generazione di visualizzazioni in Entity Framework 4 e 5 può essere eseguita con EDMGen o Entity Framework Power Tools. Per la generazione di visualizzazioni di Entity Framework 6 può essere eseguita tramite Entity Framework Power Tools o a livello di codice, come descritto in [Viste di mapping pre-generate](~/ef6/fundamentals/performance/pre-generated-views.md).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Utilizzo delle chiavi esterne anziché delle associazioni indipendenti

Quando si usa EDMGen o Entity Designer in Visual Studio, si ottengono fKs per impostazione predefinita e accetta solo una singola casella di controllo o flag della riga di comando per passare tra FK e IA.

Se si dispone di un modello Code First di grandi dimensioni, l'utilizzo di associazioni indipendenti avrà lo stesso effetto sulla generazione della vista. È possibile evitare questo impatto includendo le proprietà di chiave esterna nelle classi per gli oggetti dipendenti, anche se alcuni sviluppatori considereranno questo per inquinare il loro modello a oggetti. È possibile trovare ulteriori informazioni \< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>su questo argomento in .

| Quando si utilizza      | Procedere nel modo seguente                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Finestra di progettazione entità | Dopo aver aggiunto un'associazione tra due entità, assicurarsi di disporre di un vincolo referenziale. I vincoli referenziali indicano a Entity Framework di utilizzare le chiavi esterne anziché le associazioni indipendenti. Per ulteriori \< https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>dettagli visita . |
| EDMGen          | Quando si utilizza EDMGen per generare i file dal database, le chiavi esterne verranno rispettate e aggiunte al modello come tali. Per ulteriori informazioni sulle diverse opzioni esposte da EDMGen, visitare [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).                           |
| Code First      | Vedere la sezione "Convenzione sulle relazioni" dell'argomento [Convenzioni Code First](~/ef6/modeling/code-first/conventions/built-in.md) per informazioni su come includere le proprietà di chiave esterna negli oggetti dipendenti quando si utilizza Code First.                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Spostamento del modello in un assieme separato

Quando il modello è incluso direttamente nel progetto dell'applicazione e si generano visualizzazioni tramite un evento di pre-compilazione o un modello T4, la generazione e la convalida della visualizzazione verranno eseguite ogni volta che il progetto viene ricompilato, anche se il modello non è stato modificato. Se si sposta il modello in un assembly separato e vi si fa riferimento dal progetto dell'applicazione, è possibile apportare altre modifiche all'applicazione senza dover ricompilare il progetto contenente il modello.

*Nota:*  quando si sposta il modello in assembly separati ricordarsi di copiare le stringhe di connessione per il modello nel file di configurazione dell'applicazione del progetto client.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Disabilitare la convalida di un modello basato su edmx

I modelli EDMX vengono convalidati in fase di compilazione, anche se il modello è invariato. Se il modello è già stato convalidato, è possibile eliminare la convalida in fase di compilazione impostando la proprietà "Convalida in fase di compilazione" su false nella finestra delle proprietà. Quando si modifica il mapping o il modello, è possibile riattivare temporaneamente la convalida per verificare le modifiche.

Si noti che sono stati apportati miglioramenti delle prestazioni a Entity Framework Designer per Entity Framework 6 e il costo di "Convalida in fase di compilazione" è molto inferiore rispetto alle versioni precedenti della finestra di progettazione.

## <a name="3-caching-in-the-entity-framework"></a>3 Memorizzazione nella cache in Entity Framework

Entity Framework dispone delle seguenti forme di memorizzazione nella cache incorporate:

1.  Memorizzazione nella cache degli oggetti: ObjectStateManager incorporato in un'istanza ObjectContext tiene traccia degli oggetti recuperati utilizzando tale istanza. Questa operazione è nota anche come cache di primo livello.
2.  Memorizzazione nella cache del piano di query: riutilizzo del comando di archivio generato quando una query viene eseguita più di una volta.
3.  Memorizzazione nella cache dei metadati: condivisione dei metadati per un modello tra connessioni diverse allo stesso modello.

Oltre alle cache fornite da Entity Framework, è possibile utilizzare anche un tipo speciale di provider di dati di ADO.NET noto come provider di wrapping per estendere Entity Framework con una cache per i risultati recuperati dal database, noto anche come memorizzazione nella cache di secondo livello.

### <a name="31-object-caching"></a>3.1 Memorizzazione nella cache degli oggetti

Per impostazione predefinita, quando un'entità viene restituita nei risultati di una query, appena prima che Entity Framework la materializzi, ObjectContext controllerà se un'entità con la stessa chiave è già stata caricata nel relativo ObjectStateManager.By default when an entity is returned in the results of a query, just before EF materializes, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager. Se un'entità con le stesse chiavi è già presente Entity Framework includerà nei risultati della query. Anche se Entity Framework emetterà comunque la query sul database, questo comportamento può ignorare gran parte del costo di materializzazione dell'entità più volte.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Recupero di entità dalla cache degli oggetti tramite La ricerca DbContext

A differenza di una query normale, il metodo Find in DbSet (API incluse per la prima volta in EF 4.1) eseguirà una ricerca in memoria prima ancora di eseguire la query sul database. È importante notare che due diverse istanze ObjectContext avranno due diverse istanze di ObjectStateManager, ovvero hanno cache degli oggetti separate.

Find usa il valore della chiave primaria per tentare di trovare un'entità rilevata dal contesto. Se l'entità non si trova nel contesto, verrà eseguita una query e valutata sul database e viene restituito null se l'entità non viene trovata nel contesto o nel database. Si noti che Find restituisce anche le entità che sono state aggiunte al contesto ma non sono ancora state salvate nel database.

C'è una considerazione delle prestazioni da prendere quando si utilizza Find. Le chiamate a questo metodo per impostazione predefinita attiveranno una convalida della cache degli oggetti per rilevare le modifiche ancora in attesa di commit nel database. Questo processo può essere molto costoso se è presente un numero molto elevato di oggetti nella cache degli oggetti o in un oggetto grafico di grandi dimensioni aggiunto alla cache degli oggetti, ma può anche essere disabilitato. In alcuni casi, è possibile percepire l'ordine di grandezza della differenza nella chiamata al metodo Find quando si disabilita il rilevamento automatico delle modifiche. Tuttavia, un secondo ordine di grandezza viene percepito quando l'oggetto si trova effettivamente nella cache rispetto a quando l'oggetto deve essere recuperato dal database. Ecco un grafico di esempio con misure effettuate utilizzando alcuni dei nostri microbenchmark, espressi in millisecondi, con un carico di 5000 entità:

![Scala logaritmica .NET 4.5](~/ef6/media/net45logscale.png ".NET 4.5 - scala logaritmica")

Esempio di ricerca con rilevamento automatico delle modifiche disabilitato:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Quello che dovete considerare quando si utilizza il Find metodo è:

1.  Se l'oggetto non è nella cache, i vantaggi di Find vengono negati, ma la sintassi è ancora più semplice di una query per chiave.
2.  Se il rilevamento automatico delle modifiche è abilitato, il costo del metodo Find può aumentare di un ordine di grandezza o anche di più a seconda della complessità del modello e della quantità di entità nella cache degli oggetti.

Inoltre, tenere presente che Find restituisce solo l'entità che si sta cercando e non carica automaticamente le entità associate se non sono già presenti nella cache degli oggetti. Se è necessario recuperare le entità associate, è possibile usare una query con la chiave con caricamento eager. Per ulteriori informazioni, vedere **8.1 Caricamento lazy e caricamento eager**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Problemi di prestazioni quando la cache degli oggetti ha molte entità

La cache degli oggetti consente di aumentare la velocità di risposta complessiva di Entity Framework. Tuttavia, quando la cache degli oggetti ha una grande quantità di entità caricate può influire su determinate operazioni, ad esempio Add, Remove, Find, Entry, SaveChanges e altro ancora. In particolare, le operazioni che attivano una chiamata a DetectChanges saranno influenzate negativamente da cache di oggetti molto grandi. DetectChanges sincronizza l'oggetto grafico con il gestore dello stato degli oggetti e le relative prestazioni verranno determinate direttamente dalle dimensioni dell'oggetto grafico. Per ulteriori informazioni su DetectChanges, vedere [Rilevamento delle modifiche nelle entità POCO](https://msdn.microsoft.com/library/dd456848.aspx).

Quando si usa Entity Framework 6, gli sviluppatori sono in grado di chiamare AddRange e RemoveRange direttamente su un DbSet, anziché scorrere su una raccolta e chiamare Add una volta per istanza. Il vantaggio dell'utilizzo dei metodi di intervallo è che il costo di DetectChanges viene pagato una sola volta per l'intero set di entità anziché una volta per ogni entità aggiunta.

### <a name="32-query-plan-caching"></a>3.2 Memorizzazione nella cache del piano di query

La prima volta che viene eseguita una query, passa attraverso il compilatore piano interno per convertire la query concettuale nel comando store (ad esempio, il T-SQL che viene eseguito quando viene eseguito su SQL Server).Se la memorizzazione nella cache del piano di query è abilitata, la volta successiva che la query viene eseguita il comando di archivio viene recuperato direttamente dalla cache del piano di query per l'esecuzione, ignorando il compilatore del piano.

The query plan cache is shared across ObjectContext instances within the same AppDomain. Non è necessario mantenere un'istanza ObjectContext per trarre vantaggio dalla memorizzazione nella cache del piano di query.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 Alcune note sulla memorizzazione nella cache del piano di query

-   La cache del piano di query è condivisa per tutti i tipi di query: Entity SQLEntity SQL, LINQ to Entities e CompiledQuery oggetti.
-   Per impostazione predefinita, la memorizzazione nella cache del piano di query è abilitata per le query Entity SQLEntity SQL, sia eseguite tramite un EntityCommand o tramite objectQuery.By default, query plan caching is enabled for Entity SQL queries, whether executed through a EntityCommand or through an ObjectQuery. È inoltre abilitato per impostazione predefinita per le query LINQ to Entities in Entity Framework in .NET 4.5 e in Entity Framework 6
    -   La memorizzazione nella cache del piano di query può essere disabilitata impostando la proprietà EnablePlanCaching (in EntityCommand o ObjectQuery) su false. Ad esempio:
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
-   Per le query con parametri, la modifica del valore del parametro colpirà comunque la query memorizzata nella cache. Tuttavia, la modifica dei facet di un parametro (ad esempio, dimensioni, precisione o scala) raggiungerà una voce diversa nella cache.
-   Quando si usa Entity SQLEntity SQL, la stringa di query fa parte della chiave. La modifica della query comporterà voci della cache diverse, anche se le query sono equivalenti dal punto di lavoro. Sono incluse le modifiche alle maiuscole e minuscole o agli spazi vuoti.
-   Quando si usa LINQ, la query viene elaborata per generare una parte della chiave. La modifica dell'espressione LINQ genererà pertanto una chiave diversa.
-   Possono essere applicate altre limitazioni tecniche; Per ulteriori dettagli, vedere Query compilate automaticamente.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Algoritmo di sfratto della cache

Comprendere il funzionamento dell'algoritmo interno consente di capire quando abilitare o disabilitare la memorizzazione nella cache del piano di query. L'algoritmo di pulizia è il seguente:The cleanup algorithm is as follows:

1.  Una volta che la cache contiene un numero impostato di voci (800), viene avviato un timer che periodicamente (una volta al minuto) spazza la cache.
2.  Durante gli sweep della cache, le voci vengono rimosse dalla cache su base LFRU (meno frequentemente - utilizzati di recente). Questo algoritmo prende in considerazione sia il numero di hit che l'età quando si decide quali voci vengono espulse.
3.  Alla fine di ogni sweep della cache, la cache contiene nuovamente 800 voci.

Tutte le voci della cache vengono trattate allo stesso modo quando si determina quali voci rimuovere. Ciò significa che il comando store per un oggetto CompiledQuery ha le stesse probabilità di rimozione del comando store per una query Entity SQL.

Si noti che il timer di rimozione della cache viene avviato quando sono presenti 800 entità nella cache, ma la cache viene sottoposta a sweep solo da 60 secondi dopo l'avvio del timer. Ciò significa che per un massimo di 60 secondi la cache può aumentare fino a diventare piuttosto grande.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Metriche di test che illustrano le prestazioni di memorizzazione nella cache del piano di query

Per illustrare l'effetto della memorizzazione nella cache del piano di query sulle prestazioni dell'applicazione, è stato eseguito un test in cui sono state eseguite numerose query Entity SQLEntity SQL sul modello Navision. Vedere l'appendice per una descrizione del modello Navision e i tipi di query che sono state eseguite. In questo test, per prima cosa scorriamo l'elenco delle query ed eseguiamo ognuna una volta per aggiungerle alla cache (se la memorizzazione nella cache è abilitata). Questo passaggio non è temporizzato. Successivamente, dormiamo il filo conduttore per oltre 60 secondi per consentire lo sweep della cache; infine, scorriamo l'elenco una seconda volta per eseguire le query memorizzate nella cache. Inoltre, la cache dei piani di SQL Server viene scaricata prima dell'esecuzione di ogni set di query in modo che gli orari ottenuti riflettano in modo accurato il vantaggio offerto dalla cache del piano di query.

##### <a name="3231-test-results"></a>3.2.3.1 Risultati dei test

| Test                                                                   | EF5 nessuna cache | EF5 memorizzato nella cache | EF6 nessuna cache | EF6 memorizzato nella cache |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Enumerazione di tutte le query 18723                                          | 124          | 125.4      | 124.3        | 125.3      |
| Evitare lo sweep (solo le prime query 800, indipendentemente dalla complessità)  | 41.7         | 5.5        | 40.5         | 5.4        |
| Solo le query Aggregating Subtotalis (178 in totale - che evita sweep) | 39.5         | 4.5        | 38.1         | 4.6        |

*Tutti i tempi in pochi secondi.*

Morale: quando si eseguono molte query distinte (ad esempio, query create dinamicamente), la memorizzazione nella cache non consente di risolvere il contenuto e lo svuotamento risultante della cache può mantenere le query che trarrebbero il massimo vantaggio dal piano di memorizzazione nella cache dall'utilizzo effettiva.

Le query AggregngSubtotals sono la più complessa delle query testate con. Come previsto, quanto più complessa è la query, maggiore sarà il vantaggio che si vedrà dalla memorizzazione nella cache del piano di query.

Poiché un oggetto CompiledQuery è in realtà una query LINQ con il piano memorizzato nella cache, il confronto di un oggetto CompiledQuery con la query Entity SQL equivalente deve avere risultati simili. Infatti, se un'app dispone di molte query Entity SQL dinamiche, riempire la cache con le query causerà anche CompiledQueries per "decompilare" quando vengono scaricati dalla cache. In questo scenario, le prestazioni possono essere migliorate disabilitando la memorizzazione nella cache nelle query dinamiche per assegnare priorità a CompiledQueries. Meglio ancora, naturalmente, sarebbe quello di riscrivere l'applicazione per utilizzare query con parametri anziché query dinamiche.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 Utilizzo di CompiledQuery per migliorare le prestazioni con le query LINQ

I test indicano che l'utilizzo di CompiledQuery può portare un vantaggio del 7% rispetto alle query LINQ autocompilate; ciò significa che si spenderà il 7% in meno di tempo per l'esecuzione di codice dallo stack di Entity Framework; non significa che l'applicazione sarà 7% più veloce. In generale, il costo di scrittura e gestione di oggetti CompiledQuery in EF 5.0 potrebbe non valere la pena rispetto ai vantaggi. Il chilometraggio può variare, quindi esercita questa opzione se il tuo progetto richiede la spinta aggiuntiva. Si noti che CompiledQueries sono compatibili solo con i modelli derivati da ObjectContext e non compatibili con i modelli derivati da DbContext.Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.

Per ulteriori informazioni sulla creazione e la chiamata di un oggetto CompiledQuery, vedere [Query compilate (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Ci sono due considerazioni che è necessario prendere quando si utilizza un CompiledQuery, vale a dire il requisito di utilizzare le istanze statiche e i problemi che hanno con la componibilità. Di seguito è riportata una spiegazione approfondita di queste due considerazioni.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Utilizzare istanze compiledQuery statiche

Poiché la compilazione di una query LINQ è un processo che richiede molto tempo, non vogliamo farlo ogni volta che è necessario recuperare i dati dal database. Le istanze CompiledQuery consentono di compilare una sola volta ed eseguire più volte, ma è necessario prestare attenzione e procurarsi per riutilizzare la stessa istanza CompiledQuery ogni volta che invece di compilarla più e più volte. L'utilizzo di membri statici per archiviare il CompiledQuery istanze diventa necessario; in caso contrario non si vedrà alcun beneficio.

Si supponga, ad esempio, che la pagina disponga del seguente corpo del metodo per gestire la visualizzazione dei prodotti per la categoria selezionata:

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

In questo caso, si creerà una nuova istanza CompiledQuery in tempo reale ogni volta che viene chiamato il metodo. Invece di visualizzare i vantaggi in termini di prestazioni recuperando il comando store dalla cache del piano di query, CompiledQuery passerà attraverso il compilatore di piani ogni volta che viene creata una nuova istanza. Infatti, si inquinerà la cache del piano di query con una nuova voce CompiledQuery ogni volta che viene chiamato il metodo.

Si desidera invece creare un'istanza statica della query compilata, in modo da richiamare la stessa query compilata ogni volta che viene chiamato il metodo. Un modo per questo è aggiungendo il CompiledQuery istanza come membro del contesto dell'oggetto.È quindi possibile rendere le cose un po ' più pulito accedendo a CompiledQuery tramite un metodo helper:You can then make things a little cleaner by accessing the CompiledQuery through a helper method:

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

Questo metodo di supporto viene richiamato come segue:This helper method would be invoked as follows:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Composizione su una query di compilazione

La possibilità di comporre su qualsiasi query LINQ è estremamente utile; A tale scopo, è sufficiente richiamare un metodo dopo iQueryable, ad esempio *Skip()* o *Count().* Tuttavia, questa operazione restituisce essenzialmente un nuovo oggetto IQueryable.However, doing so essentially returns a new IQueryable object. Anche se non c'è nulla che ti impedisca tecnicamente di comporre su un oggetto CompiledQuery, in questo modo la generazione di un nuovo oggetto IQueryable che richiede nuovamente il passaggio attraverso il compilatore di piano.

Alcuni componenti utilizzeranno oggetti IQueryable composti per abilitare funzionalità avanzate. Ad esempio, ASP. GridView di NET può essere associato a dati a un IQueryable oggetto tramite il SelectMethod proprietà. Il controllo GridView comporrà quindi su questo oggetto IQueryable per consentire l'ordinamento e il paging sul modello di dati. Come si può vedere, utilizzando un CompiledQuery per il controllo GridView non sarebbe raggiunto la query compilata, ma genererebbe una nuova query automatica.

Un punto in cui è possibile eseguire questo è quando si aggiungono filtri progressivi a una query. Si supponga, ad esempio, di avere una pagina Clienti con diversi elenchi a discesa per i filtri facoltativi , ad esempio Country e OrdersCount. È possibile comporre questi filtri sui risultati IQueryable di un oggetto CompiledQuery, ma in questo modo la nuova query passa attraverso il compilatore di piani ogni volta che viene eseguita.

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

 Per evitare questa ricompilazione, è possibile riscrivere il CompiledQuery per prendere in considerazione i possibili filtri:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Che verrebbe richiamato nell'interfaccia utente come:

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

 Un compromesso è che il comando di archivio generato avrà sempre i filtri con i controlli null, ma questi dovrebbero essere abbastanza semplici per il server di database per ottimizzare:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 Memorizzazione dei metadati nella cache

Entity Framework supporta anche la memorizzazione dei metadati nella cache. Si tratta essenzialmente di memorizzazione nella cache delle informazioni sul tipo e del mapping da tipo a database tra connessioni diverse allo stesso modello. La cache dei metadati è univoca per AppDomain.The Metadata cache is unique per AppDomain.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Algoritmo di memorizzazione dei metadati Caching

1.  Le informazioni sui metadati per un modello vengono archiviate in un ItemCollection per ogni EntityConnection.Metadata information for a model is stored in an ItemCollection for each EntityConnection.
    -   Come nota a margine, esistono diversi ItemCollection oggetti per le diverse parti del modello. Ad esempio, StoreItemCollections contiene le informazioni sul modello di database; ObjectItemCollection contiene informazioni sul modello di dati; EdmItemCollection contiene informazioni sul modello concettuale.

2.  Se due connessioni utilizzano la stessa stringa di connessione, condivideranno la stessa istanza ItemCollection.If two connections use the same connection string, they will share the same ItemCollection instance.
3.  Le stringhe di connessione funzionalmente equivalenti ma clauviamente diverse possono generare cache dei metadati diverse. Facciamo tokenize stringhe di connessione, quindi semplicemente la modifica dell'ordine dei token dovrebbe comportare metadati condivisi. Ma due stringhe di connessione che sembrano funzionalmente uguali potrebbero non essere valutate come identiche dopo la tokenizzazione.
4.  Il ItemCollection viene controllato periodicamente per l'utilizzo. Se viene determinato che un workspace non è stato eseguito di recente, verrà contrassegnato per la pulizia nella successiva sweep della cache.
5.  La semplice creazione di un oggetto EntityConnection causerà la creazione di una cache dei metadati (anche se le raccolte di elementi in essa in essa in essa non verranno inizializzate fino all'apertura della connessione). Questa area di lavoro rimarrà in memoria fino a quando l'algoritmo di memorizzazione nella cache non determina che non è "in uso".

Il team di consulenza clienti ha scritto un post di blog che descrive il mantenimento di \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>un riferimento a un oggetto ItemCollection per evitare la "deprecazione" quando si utilizzano modelli di grandi dimensioni: .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 Relazione tra la memorizzazione nella cache dei metadati e la memorizzazione nella cache del piano di query

L'istanza della cache del piano di query si trova nella raccolta ItemCollection di MetadataWorkspace dei tipi di archivio. Ciò significa che i comandi dell'archivio memorizzati nella cache verranno utilizzati per le query su qualsiasi contesto di cui è stata creata un'istanza utilizzando un oggetto MetadataWorkspace specificato. Significa anche che se si dispone di due stringhe di connessioni che sono leggermente diverse e non corrispondono dopo il tokenizzazione, si disporrà di istanze della cache del piano di query diverse.

### <a name="35-results-caching"></a>3.5 Memorizzazione nella cache dei risultati

Con la memorizzazione nella cache dei risultati (nota anche come "memorizzazione nella cache di secondo livello"), si mantengono i risultati delle query in una cache locale. Quando si esegue una query, è necessario verificare se i risultati sono disponibili localmente prima di eseguire una query sull'archivio. Mentre la memorizzazione dei risultati nella cache non è supportata direttamente da Entity Framework, è possibile aggiungere una cache di secondo livello utilizzando un provider di wrapping. Un provider di cui avviene di esempio con una cache di secondo livello è la cache di secondo livello di Entity Framework di Alachisoft [basata su NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Questa implementazione della memorizzazione nella cache di secondo livello è una funzionalità inserita che viene eseguita dopo che l'espressione LINQ è stata valutata (e migliorata) e il piano di esecuzione della query viene calcolato o recuperato dalla cache di primo livello. La cache di secondo livello archivierà quindi solo i risultati del database non elaborati, in modo che la pipeline di materializzazione venga comunque eseguita successivamente.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Riferimenti aggiuntivi per la memorizzazione nella cache dei risultati con il provider di wrapping

-   Julie Lerman ha scritto un articolo MSDN "Second-Level Caching in Entity Framework and Windows Azure" che include come aggiornare il provider di wrapping di esempio per utilizzare la memorizzazione nella cache di Windows Server AppFabric:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Se si utilizza Entity Framework 5, il blog del team contiene un post che descrive come \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>eseguire elementi con il provider di memorizzazione nella cache per Entity Framework 5: . Include anche un modello T4 per automatizzare l'aggiunta della memorizzazione nella cache di secondo livello al progetto.

## <a name="4-autocompiled-queries"></a>4 Query autocompilate

Quando una query viene eseguita su un database che utilizza Entity Framework, deve eseguire una serie di passaggi prima di materializzare effettivamente i risultati; uno di questi passaggi è la compilazione di query. Le query Entity SQLEntity SQL erano note per avere buone prestazioni in quanto vengono automaticamente memorizzate nella cache, pertanto la seconda o la terza volta che si esegue la stessa query è possibile ignorare il compilatore di piano e utilizzare il piano memorizzato nella cache invece.

Entity Framework 5 ha introdotto la memorizzazione automatica nella cache anche per le query LINQ to Entities. Nelle edizioni precedenti di Entity Framework la creazione di un oggetto CompiledQuery per velocizzare le prestazioni era una pratica comune, in quanto ciò renderebbe la query LINQ to Entities memorizzabile nella cache. Poiché la memorizzazione nella cache viene ora eseguita automaticamente senza l'utilizzo di un oggetto CompiledQuery, questa funzionalità viene chiamata "query compilate automaticamente". Per altre informazioni sulla cache del piano di query e sui relativi meccanismi, vedere Memorizzazione nella cache del piano di query.

Entity Framework rileva quando una query richiede la ricompilazione e lo fa quando la query viene richiamata anche se era stata compilata in precedenza. Le condizioni comuni che causano la ricompilazione della query sono:

-   Modifica dell'opzione MergeOption associata alla query. La query memorizzata nella cache non verrà utilizzata, ma il compilatore di piani verrà eseguito di nuovo e il piano appena creato viene memorizzato nella cache.
-   Modifica del valore di ContextOptions.UseCSharpNullComparisonBehavior. Si ottiene lo stesso effetto della modifica di MergeOption.You get the same effect as changing the MergeOption.

Altre condizioni possono impedire alla query di utilizzare la cache. Esempi comuni:

-   Utilizzo di&lt;&gt;IEnumerable T . Contiene&lt;&gt;(valore T).
-   Utilizzo di funzioni che producono query con costanti.
-   Utilizzo delle proprietà di un oggetto non mappato.
-   Collegamento della query a un'altra query che deve essere ricompilata.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Utilizzo&lt;di&gt;IEnumerable T . Contiene&lt;&gt;T (valore T)

Entity Framework non memorizza nella&lt;&gt;cache le query che richiamano IEnumerable T . Contiene&lt;&gt;T (valore T) rispetto a una raccolta in memoria, poiché i valori della raccolta sono considerati volatili. La query di esempio seguente non verrà memorizzata nella cache, pertanto verrà sempre elaborata dal compilatore del piano:The following example query will not be cached, so it will always be processed by the plan compiler:

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

Si noti che la dimensione di IEnumerable su cui Contains viene eseguito determina la velocità o la velocità di compilazione della query. Le prestazioni possono risentirne in modo significativo quando si usano raccolte di grandi dimensioni, come quella illustrata nell'esempio precedente.

Entity Framework 6 contiene ottimizzazioni&lt;per&gt;il modo in cui IEnumerable T . Contiene&lt;&gt;il valore T (valore T) funziona quando vengono eseguite le query. Il codice SQL generato è molto più veloce da produrre e più leggibile e nella maggior parte dei casi viene eseguito anche più velocemente nel server.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 Utilizzo di funzioni che producono query con costanti

Gli operatori LINQ Skip(), Take(), Contains() e DefautIfEmpty() non producono query SQL con parametri, ma inseriscono i valori passati come costanti. Per questo motivo, le query che potrebbero altrimenti essere identiche finiscono per inquinare la cache del piano di query, sia nello stack di Entity Framework e sul server di database e non vengono reutilizzate a meno che le stesse costanti vengono utilizzate in un'esecuzione di query successiva. Ad esempio:

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

In questo esempio, ogni volta che la query viene eseguita con un valore diverso per id la query verrà compilata in un nuovo piano.

In particolare prestare attenzione all'uso di Skip and Take quando si esegue il paging. In EF6 questi metodi hanno un overload lambda che rende in modo efficace il piano di query memorizzato nella cache riutilizzabile perché EF può acquisire le variabili passate a questi metodi e tradurle in SQLparameters. Ciò consente inoltre di mantenere la cache più pulita poiché in caso contrario ogni query con una costante diversa per Skip e Take otterrebbe la propria voce della cache del piano di query.

Si consideri il codice seguente, che non è ottimale, ma ha solo lo scopo di esemplificare questa classe di query:Consider the following code, which is suboptimal but is only meant to emplify this class of queries:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Una versione più veloce di questo stesso codice comporterebbe la chiamata Skip con un'espressione lambda:A faster version of this same code would involve calling Skip with a lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Il secondo frammento di codice può essere eseguito fino all'11% più velocemente perché viene utilizzato lo stesso piano di query ogni volta che viene eseguita la query, risparmiando tempo della CPU ed evitando di inquinare la cache delle query. Inoltre, poiché il parametro Skip è in una chiusura, il codice potrebbe anche essere simile al seguente ora:For the parameter to Skip is in a closure, the code might well look like this now:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 Utilizzo delle proprietà di un oggetto non mappato

Quando una query utilizza le proprietà di un tipo di oggetto non mappato come parametro, la query non verrà memorizzata nella cache. Ad esempio:

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

In questo esempio, si supponga che la classe NonMappedType non faccia parte del modello di entità. Questa query può essere facilmente modificata per non utilizzare un tipo non mappato e invece utilizzare una variabile locale come parametro per la query:This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:

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

In questo caso, la query sarà in grado di ottenere la memorizzazione nella cache e trarrà vantaggio dalla cache del piano di query.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 Collegamento a query che richiedono la ricompilazione

Seguendo lo stesso esempio precedente, se si dispone di una seconda query che si basa su una query che deve essere ricompilata, verrà ricompilata anche l'intera seconda query. Di seguito è riportato un esempio per illustrare questo scenario:Here's an example to illustrate this scenario:

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

L'esempio è generico, ma illustra come il collegamento a firstQuery stia causando la impossibilità di non essere memorizzata nella cache di secondQuery.The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached. Se firstQuery non fosse stata una query che richiede la ricompilazione, secondQuery sarebbe stata memorizzata nella cache.

## <a name="5-notracking-queries"></a>5 Query NoTracking

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 Disabilitazione del rilevamento delle modifiche per ridurre l'overhead di gestione dello stato

Se si è in uno scenario di sola lettura e si desidera evitare l'overhead del caricamento degli oggetti in ObjectStateManager, è possibile eseguire query "No Tracking".Il rilevamento delle modifiche può essere disabilitato a livello di query.

Si noti tuttavia che disabilitando il rilevamento delle modifiche si disattiva effettivamente la cache degli oggetti. Quando si esegue una query per un'entità, non è possibile ignorare la materializzazione estraendo i risultati della query materializzata in precedenza da ObjectStateManager. Se si esegue ripetutamente una query per le stesse entità nello stesso contesto, è possibile ottenere un vantaggio in termini di prestazioni derivante dall'abilitazione del rilevamento delle modifiche.

Quando si esegue una query utilizzando ObjectContext, ObjectQuery e ObjectSet istanze ricorderanno un MergeOption una volta che è impostato e le query che sono composte su di esse erediteranno l'effettivo MergeOption della query padre. Quando si utilizza DbContext, il rilevamento può essere disabilitato chiamando il modificatore AsNoTracking() nel DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Disabilitazione del rilevamento delle modifiche per una query quando si usa DbContext

È possibile passare alla modalità di una query su NoTracking concatenando una chiamata al metodo AsNoTracking() nella query. A differenza di ObjectQuery, le classi DbSet e DbQuery nell'API DbContext non dispongono di una proprietà modificabile per MergeOption.

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

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Disabilitazione del rilevamento delle modifiche per un intero set di entità tramite ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 Metriche di test che illustrano i vantaggi in termini di prestazioni delle query NoTracking

In questo test esamineremo il costo di riempimento di ObjectStateManager confrontando Tracking a NoTracking query per il modello Navision.In this test we look the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model. Vedere l'appendice per una descrizione del modello Navision e i tipi di query che sono state eseguite. In questo test, iterare l'elenco di query ed eseguirle una volta. Abbiamo eseguito due varianti del test, una volta con le query NoTracking e una volta con l'opzione di unione predefinita di "AppendOnly". Abbiamo eseguito ogni variazione 3 volte e prendere il valore medio delle corse. Tra i test si cancella la cache delle query in SQL Server e compattare il database tempdb eseguendo i comandi seguenti:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Risultati del test, mediana su 3 corse:

|                        | NESSUN TRACCIAMENTO – WORKING SET | NESSUN TRACCIAMENTO – TEMPO | SOLO AGGIUNTA – WORKING SET | SOLO APPEND – TEMPO |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 avrà un footprint di memoria inferiore alla fine dell'esecuzione rispetto a Entity Framework 6. La memoria aggiuntiva utilizzata da Entity Framework 6 è il risultato di strutture di memoria aggiuntive e codice che consentono nuove funzionalità e prestazioni migliori.

C'è anche una chiara differenza nel footprint di memoria quando si utilizza ObjectStateManager. Entity Framework 5 ha aumentato il footprint del 30% tenendo traccia di tutte le entità che abbiamo materializzato dal database. Entity Framework 6 ha aumentato la propria impronta del 28% quando lo si fa.

In termini di tempo, Entity Framework 6 supera Entity Framework 5 in questo test di un margine elevato. Entity Framework 6 ha completato il test in circa il 16% del tempo impiegato da Entity Framework 5. Inoltre, Entity Framework 5 richiede il 9% di tempo in più per completare quando il ObjectStateManager viene utilizzato. In confronto, Entity Framework 6 utilizza il 3% di tempo in più quando si utilizza ObjectStateManager.

## <a name="6-query-execution-options"></a>6 Opzioni di esecuzione delle query

Entity Framework offre diversi modi per eseguire query. Esamineremo le seguenti opzioni, confronteremo i pro e i contro di ciascuna ed esamineremo le loro caratteristiche di prestazione:

-   LINQ to Entities.
-   Nessun tracking LINQ to Entities.
-   Entity SQL su un oggetto ObjectQuery.
-   Entity SQL su un EntityCommand.
-   ExecuteStoreQuery.
-   Oggetto SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 Query LINQ to Entities

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Pro**

-   Adatto per le operazioni CUD.
-   Oggetti completamente materializzati.
-   Più semplice scrivere con la sintassi incorporata nel linguaggio di programmazione.
-   Buone prestazioni.

**Contro**

-   Alcune restrizioni tecniche, quali:
    -   I modelli che usano DefaultIfEmpty per le query OUTER JOIN generano query più complesse rispetto alle istruzioni OUTER JOIN semplici in Entity SQLEntity SQL.
    -   Non è ancora possibile usare LIKE con criteri di ricerca generali.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 Nessun rilevamento di query LINQ to Entities

Quando il contesto deriva ObjectContext:When the context derives ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Quando il contesto deriva DbContext:When the context derives DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Pro**

-   Miglioramento delle prestazioni rispetto alle normali query LINQ.
-   Oggetti completamente materializzati.
-   Più semplice scrivere con la sintassi incorporata nel linguaggio di programmazione.

**Contro**

-   Non adatto per le operazioni CUD.
-   Alcune restrizioni tecniche, quali:
    -   I modelli che usano DefaultIfEmpty per le query OUTER JOIN generano query più complesse rispetto alle istruzioni OUTER JOIN semplici in Entity SQLEntity SQL.
    -   Non è ancora possibile usare LIKE con criteri di ricerca generali.

Si noti che le query che proiettano le proprietà scalari non vengono rilevate anche se NoTracking non è specificato. Ad esempio:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Questa particolare query non specifica in modo esplicito di NoTracking, ma poiché non sta materializzando un tipo noto al gestore dello stato dell'oggetto, il risultato materializzato non viene registrato.

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 Entity SQL su un oggetto ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Pro**

-   Adatto per le operazioni CUD.
-   Oggetti completamente materializzati.
-   Supporta la memorizzazione nella cache del piano di query.

**Contro**

-   Coinvolge stringhe di query testuali che sono più soggette a errori dell'utente rispetto ai costrutti di query incorporati nel linguaggio.

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Entity SQL su un comando di entità

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

**Pro**

-   Supporta la memorizzazione nella cache del piano di query in .NET 4.0 (la memorizzazione nella cache del piano è supportata da tutti gli altri tipi di query in .NET 4.5).

**Contro**

-   Coinvolge stringhe di query testuali che sono più soggette a errori dell'utente rispetto ai costrutti di query incorporati nel linguaggio.
-   Non adatto per le operazioni CUD.
-   I risultati non vengono materializzati automaticamente e devono essere letti dal lettore dati.

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery ed ExecuteStoreQuery

SqlQuery nel database:

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

**Pro**

-   Prestazioni generalmente più veloci poiché il compilatore del piano viene ignorato.
-   Oggetti completamente materializzati.
-   Adatto per le operazioni CUD quando utilizzato dal DbSet.

**Contro**

-   La query è testuale e soggetta a errori.
-   La query è legata a un back-end specifico utilizzando la semantica dell'archivio anziché la semantica concettuale.
-   Quando è presente l'ereditarietà, la query creata a mano deve tenere conto delle condizioni di mapping per il tipo richiesto.

### <a name="66-compiledquery"></a>6.6 Database CompilatoQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Pro**

-   Fornisce fino a un miglioramento delle prestazioni del 7% rispetto alle normali query LINQ.
-   Oggetti completamente materializzati.
-   Adatto per le operazioni CUD.

**Contro**

-   Aumento della complessità e dell'overhead di programmazione.
-   Il miglioramento delle prestazioni viene perso durante la composizione su una query compilata.
-   Alcune query LINQ non possono essere scritte come CompiledQuery, ad esempio proiezioni di tipi anonimi.

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 Confronto delle prestazioni tra diverse opzioni di query

Sono stati sottoposti alla prova semplici microbenchmark in cui la creazione del contesto non è stata eseguita. Abbiamo misurato l'esecuzione di query 5000 volte per un set di entità non memorizzate nella cache in un ambiente controllato. Questi numeri devono essere presi con un avvertimento: non riflettono i numeri effettivi prodotti da un'applicazione, ma sono invece una misura molto accurata di quanta differenza di prestazioni c'è quando diverse opzioni di query vengono confrontate mele a mele, escludendo il costo della creazione di un nuovo contesto.

| EF  | Test                                 | Ora (ms) | Memoria   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq Query             | 2692      | 38277120 |
| EF5 | DbContext Linq Query Nessun rilevamento     | 2818      | 41840640 |
| EF5 | DbContext Linq Query                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq Query Nessun rilevamento | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq Query             | 3074      | 45248512 |
| EF6 | DbContext Linq Query Nessun rilevamento     | 3125      | 47575040 |
| EF6 | DbContext Linq Query                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq Query Nessun rilevamento | 3593      | 45260800 |

![Micro benchmark EF5, 5000 iterazioni calde](~/ef6/media/ef5micro5000warm.png)

![Micro benchmark EF6, 5000 iterazioni calde](~/ef6/media/ef6micro5000warm.png)

I microbenchmark sono molto sensibili alle piccole modifiche nel codice. In questo caso, la differenza tra i costi di Entity Framework 5 ed Entity Framework 6 è dovuta all'aggiunta di intercettazioni e [miglioramenti transazionali.](~/ef6/saving/transactions.md) [interception](~/ef6/fundamentals/logging-and-interception.md) Questi numeri di microbenchmark, tuttavia, sono una visione amplificata in un frammento molto piccolo di ciò che Entity Framework fa. Scenari reali di query a caldo non devono visualizzare una regressione delle prestazioni durante l'aggiornamento da Entity Framework 5 a Entity Framework 6.

Per confrontare le prestazioni reali delle diverse opzioni di query, abbiamo creato 5 varianti di test separate in cui utilizziamo un'opzione di query diversa per selezionare tutti i prodotti il cui nome di categoria è "Beverages". Ogni iterazione include il costo di creazione del contesto e il costo di materializzazione di tutte le entità restituite. 10 iterazioni vengono eseguite senza tempo prima di prendere la somma di 1000 iterazioni temportime. I risultati mostrati sono l'esecuzione mediana tratta da 5 esecuzioni di ogni test. Per ulteriori informazioni, vedere l'Appendice B che include il codice per il test.

| EF  | Test                                        | Ora (ms) | Memoria   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Comando entità ObjectContext                | 621       | 39350272 |
| EF5 | DbContext Sql Query on Database             | 825       | 37519360 |
| EF5 | Query dell'archivio ObjectContext                   | 878       | 39460864 |
| EF5 | ObjectContext Linq Query Nessun rilevamento        | 969       | 38293504 |
| EF5 | ObjectContext Entity Sql using Object Query | 1089      | 38981632 |
| EF5 | Query compilata ObjectContext                | 1099      | 38682624 |
| EF5 | ObjectContext Linq Query                    | 1152      | 38178816 |
| EF5 | DbContext Linq Query Nessun rilevamento            | 1208      | 41803776 |
| EF5 | Query sql DbContext su DbSet                | 1414      | 37982208 |
| EF5 | DbContext Linq Query                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Comando entità ObjectContext                | 480       | 47247360 |
| EF6 | Query dell'archivio ObjectContext                   | 493       | 46739456 |
| EF6 | DbContext Sql Query on Database             | 614       | 41607168 |
| EF6 | ObjectContext Linq Query Nessun rilevamento        | 684       | 46333952 |
| EF6 | ObjectContext Entity Sql using Object Query | 767       | 48865280 |
| EF6 | Query compilata ObjectContext                | 788       | 48467968 |
| EF6 | DbContext Linq Query Nessun rilevamento            | 878       | 47554560 |
| EF6 | ObjectContext Linq Query                    | 953       | 47632384 |
| EF6 | Query sql DbContext su DbSet                | 1023      | 41992192 |
| EF6 | DbContext Linq Query                        | 1290      | 47529984 |


![Query a caldo di EF5 1000 iterazioni](~/ef6/media/ef5warmquery1000.png)

![Query a caldo EF6 1000 iterazioni](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Per completezza, è stata inclusa una variante in cui viene eseguita una query Entity SQL su un EntityCommand.For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand. Tuttavia, poiché i risultati non sono materializzati per tali query, il confronto non è necessariamente mele a mele. Il test include una stretta approssimazione alla materializzazione per cercare di rendere il confronto più equo.

In questo caso end-to-end, Entity Framework 6 supera Entity Framework 5 a causa di miglioramenti delle prestazioni apportati in diverse parti dello stack, tra cui un'inizializzazione DbContext molto più leggero e più veloci ricerche di MetadataCollection&lt;T.&gt;

## <a name="7-design-time-performance-considerations"></a>7 Considerazioni sulle prestazioni del tempo di progettazione

### <a name="71-inheritance-strategies"></a>7.1 Strategie di ereditarietà

Un'altra considerazione sulle prestazioni quando si utilizza Entity Framework è la strategia di ereditarietà utilizzata. Entity Framework supporta 3 tipi di ereditarietà di base e le relative combinazioni:

-   Tabella per gerarchia (TPH): in cui ogni set di ereditarietà viene mappato a una tabella con una colonna discriminatore per indicare quale particolare tipo nella gerarchia viene rappresentato nella riga.
-   Tabella per tipo (TPT), in cui ogni tipo dispone di una propria tabella nel database. le tabelle figlio definiscono solo le colonne che la tabella padre non contiene.
-   Tabella per classe (TPC), in cui ogni tipo dispone di una tabella completa nel database. le tabelle figlio definiscono tutti i relativi campi, inclusi quelli definiti nei tipi padre.

Se il modello utilizza l'ereditarietà TPT, le query generate saranno più complesse di quelle generate con le altre strategie di ereditarietà, che possono comportare tempi di esecuzione più lunghi nell'archivio.In genere, la generazione di query su un modello TPT e la materializzare gli oggetti risultanti richiede più tempo.

Vedere il post di blog MSDN "Considerazioni sulle prestazioni quando si \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>utilizza l'ereditarietà TPT (tabella per tipo) in Entity Framework: .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Evitare il TPT nelle applicazioni Model First o Code First

Quando si crea un modello su un database esistente che dispone di uno schema TPT, non sono disponibili molte opzioni. Tuttavia, quando si crea un'applicazione utilizzando Model First o Code First, è consigliabile evitare l'ereditarietà TPT per problemi di prestazioni.

Quando si utilizza Model First nella procedura guidata Entity Designer, si otterrà TPT per qualsiasi ereditarietà nel modello. Se si desidera passare a una strategia di ereditarietà TPH con Model First, è possibile \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)utilizzare il "Entity Designer Database Generation Power Pack" disponibile in Visual Studio Gallery ( .

Quando si usa Code First per configurare il mapping di un modello con ereditarietà, EF utilizzerà TPH per impostazione predefinita, pertanto tutte le entità nella gerarchia di ereditarietà verranno mappate alla stessa tabella. Vedere la sezione "Mapping con l'API Fluent" dell'articolo "Code First [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)in Entity Framework4.1" in MSDN Magazine ( ) per ulteriori dettagli.

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 Aggiornamento da EF4 per migliorare i tempi di generazione del modello

Un miglioramento specifico di SQL Server per l'algoritmo che genera il livello di archivio (SSDL) del modello è disponibile in Entity Framework 5 e 6 e come aggiornamento di Entity Framework 4 quando è installato Visual Studio 2010 SP1. I seguenti risultati dei test dimostrano il miglioramento durante la generazione di un modello molto grande, in questo caso il modello Navision. Per ulteriori informazioni, vedere l'Appendice C.

Il modello contiene 1005 set di entità e 4227 set di associazioni.

| Configurazione                              | Suddivisione del tempo consumato                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | Generazione SSDL: 2 ore 27 min <br/> Generazione mappatura: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione vista: 2 h 14 min |
| Visual Studio 2010 SP1, Entity Framework 4 | Generazione SSDL: 1 secondo <br/> Generazione mappatura: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione vista: 1 ora 53 min   |
| Visual Studio 2013, Entity Framework 5     | Generazione SSDL: 1 secondo <br/> Generazione mappatura: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione vista: 65 minuti    |
| Visual Studio 2013, Entity Framework 6     | Generazione SSDL: 1 secondo <br/> Generazione mappatura: 1 secondo <br/> Generazione CSDL: 1 secondo <br/> Generazione ObjectLayer: 1 secondo <br/> Generazione vista: 28 secondi.   |


Vale la pena notare che durante la generazione di SSDL, il carico viene quasi interamente speso su SQL Server, mentre il computer di sviluppo client è in attesa di inattività per i risultati a tornare dal server. Gli amministratori di database dovrebbero apprezzare in modo particolare questo miglioramento. Vale anche la pena notare che essenzialmente l'intero costo della generazione del modello avviene ora in Generazione vista.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 Suddivisione di modelli di grandi dimensioni con Database First e Model First

Con l'aumentare delle dimensioni del modello, l'area di progettazione diventa ingombra e difficile da usare. In genere si considera un modello con più di 300 entità troppo grande per poter usare in modo efficace la finestra di progettazione. Nel post di blog seguente vengono descritte \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>diverse opzioni per la suddivisione di modelli di grandi dimensioni: .

Il post è stato scritto per la prima versione di Entity Framework, ma i passaggi sono ancora validi.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 Considerazioni sulle prestazioni con il controllo origine dati entità

Sono stati riscontrati casi in test di prestazioni e stress multithreading in cui le prestazioni di un'applicazione Web che utilizza il controllo EntityDataSource si deteriorano in modo significativo. La causa sottostante è che EntityDataSource chiama ripetutamente MetadataWorkspace.LoadFromAssembly sugli assembly a cui fa riferimento l'applicazione Web per individuare i tipi da utilizzare come entità.

La soluzione consiste nell'impostare il ContextTypeName di EntityDataSource sul nome del tipo della classe ObjectContext derivata. In questo modo viene disattivato il meccanismo che analizza tutti gli assembly a cui si fa riferimento per i tipi di entità.

L'impostazione del campo ContextTypeName impedisce inoltre un problema funzionale in cui EntityDataSource in .NET 4.0 genera un'eccezione ReflectionTypeLoadException quando non è possibile caricare un tipo da un assembly tramite reflection. Questo problema è stato risolto in .NET 4.5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 Entità POCO e proxy di rilevamento delle modifiche

Entity Framework consente di utilizzare classi di dati personalizzate con il modello di dati senza apportare modifiche alle classi di dati stesse. Pertanto è possibile pertanto utilizzare oggetti POCO (Plain-Old CLR Object), ad esempio gli oggetti di dominio esistenti, con il modello di dati. Queste classi di dati POCO (note anche come oggetti ignora di persistenza), mappate a entità definite in un modello di dati, supportano la maggior parte delle stesse query, inserimento, aggiornamento ed eliminazione dei comportamenti generati dagli strumenti Entity Data Model.

Entity Framework può anche creare classi proxy derivate dai tipi POCO, che vengono utilizzate quando si desidera abilitare funzionalità quali il caricamento lazy e il rilevamento automatico delle modifiche nelle entità POCO. Le classi POCO devono soddisfare determinati requisiti per consentire a Entity [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)Framework di utilizzare i proxy, come descritto di seguito: .

I proxy di rilevamento delle probabilità invieranno una notifica al gestore dello stato degli oggetti ogni volta che il valore di una delle proprietà delle entità viene modificato, pertanto Entity Framework conosce sempre lo stato effettivo delle entità. Questa operazione viene eseguita aggiungendo eventi di notifica al corpo dei metodi setter delle proprietà e facendo in modo che il gestore dello stato degli oggetti eserisse tali eventi. Si noti che la creazione di un'entità proxy sarà in genere più costosa rispetto alla creazione di un'entità POCO non proxy a causa dell'aggiunta del set di eventi creato da Entity Framework.

Quando un'entità POCO non dispone di un proxy di rilevamento delle modifiche, le modifiche vengono rilevate confrontando il contenuto delle entità con una copia di uno stato salvato precedente. Questo confronto completo diventerà un processo lungo quando si dispone di molte entità nel contesto o quando le entità hanno una grande quantità di proprietà, anche se nessuna di esse è stata modificata dall'ultimo confronto.

In sintesi: al momento della creazione del proxy di rilevamento delle modifiche pagherai un calo delle prestazioni, ma il rilevamento delle modifiche ti aiuterà ad accelerare il processo di rilevamento delle modifiche quando le entità hanno molte proprietà o quando hai molte entità nel modello. Per le entità con un numero ridotto di proprietà in cui la quantità di entità non aumenta troppo, la presenza di proxy di rilevamento delle modifiche potrebbe non essere molto vantaggiosa.

## <a name="8-loading-related-entities"></a>8 Caricamento di entità correlate

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 Caricamento lazy e caricamento eager

Entity Framework offre diversi modi per caricare le entità correlate all'entità di destinazione. Ad esempio, quando si esegue una query per prodotti, esistono diversi modi in cui gli ordini correlati verranno caricati in Gestione stato oggetti. Dal punto di vista delle prestazioni, la domanda più importante da considerare quando si caricano entità correlate sarà se usare Il caricamento lazy o il caricamento eager.

Quando si usa Caricamento eager, le entità correlate vengono caricate insieme al set di entità di destinazione. Utilizzare un'istruzione Include nella query per indicare quali entità correlate si desidera importare.

Quando si usa Il caricamento lazy, la query iniziale include solo il set di entità di destinazione. Ma ogni volta che si accede a una proprietà di navigazione, viene eseguita un'altra query sull'archivio per caricare l'entità correlata.

Una volta caricata un'entità, tutte le ulteriori query per l'entità la caricheranno direttamente da Gestione stato oggetti, sia che si utilizzi il caricamento lazy o il caricamento eager.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 Come scegliere tra Caricamento lazy e Caricamento Eager

L'importante è comprendere la differenza tra Caricamento lazy e Caricamento eager in modo da poter fare la scelta corretta per l'applicazione. In questo modo sarà possibile valutare il compromesso tra più richieste rispetto al database rispetto a una singola richiesta che può contenere un payload di grandi dimensioni. Potrebbe essere appropriato usare il caricamento eager in alcune parti dell'applicazione e il caricamento lazy in altre parti.

Come esempio di ciò che sta accadendo sotto il cofano, supponiamo di voler interrogare per i clienti che vivono nel Regno Unito e il loro conteggio degli ordini.

**Utilizzo del caricamento eagerUsing Eager Loading**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Utilizzo del caricamento lazyUsing Lazy Loading**

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

Quando si utilizza il caricamento eager, si eseguirà una singola query che restituisce tutti i clienti e tutti gli ordini. Il comando store ha il seguente aspetto:

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

Quando si utilizza il caricamento lazy, si eseguirà inizialmente la query seguente:

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

E ogni volta che si accede alla proprietà di navigazione Orders di un cliente viene eseguita un'altra query simile alla seguente sul negozio:

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

Per ulteriori informazioni, vedere [Caricamento di oggetti correlati](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Caricamento pigro contro foglio trucco caricamento Eager

Non esiste una cosa adatta a tutti a scegliere il caricamento eager rispetto al caricamento pigro. Cercate prima di tutto di capire le differenze tra entrambe le strategie in modo da poter fare una decisione ben informata; Inoltre, considerare se il codice si adatta a uno dei seguenti scenari:

| Scenario                                                                    | Il nostro suggerimento                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| È necessario accedere a molte proprietà di navigazione dalle entità recuperate? | **No** - Probabilmente funzioneranno entrambe le opzioni. Tuttavia, se il payload che la query sta portando non è troppo grande, è possibile che si verifichino vantaggi in termini di prestazioni utilizzando il caricamento eager in quanto richiederà meno round trip di rete per materializzare gli oggetti. <br/> <br/> **Sì:** se è necessario accedere a molte proprietà di navigazione dalle entità, è possibile farlo usando più istruzioni di inclusione nella query con caricamento eager. Più entità includi, maggiore è il payload restituito dalla query. Dopo aver incluso tre o più entità nella query, è consigliabile passare al caricamento lazy. |
| Sai esattamente quali dati saranno necessari in fase di esecuzione?                   | **No** - Il caricamento pigro sarà meglio per te. In caso contrario, potresti finire per eseguire query per i dati che non ti serviranno. <br/> <br/> **Sì** - Il caricamento eager è probabilmente la soluzione migliore; aiuterà a caricare interi set più velocemente. Se la query richiede il recupero di una grande quantità di dati e questo diventa troppo lento, provare invece a caricare lazy.                                                                                                                                                                                                                                                       |
| Il codice è in esecuzione lontano dal database? (aumento della latenza di rete)  | **No:** quando la latenza di rete non è un problema, l'uso del caricamento differito può semplificare il codice. Tenere presente che la topologia dell'applicazione può cambiare, pertanto non dare per scontata la prossimità del database. <br/> <br/> **Sì** - Quando la rete è un problema, solo tu puoi decidere cosa si adatta meglio al tuo scenario. In genere il caricamento eager sarà migliore perché richiede meno round trip.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Problemi di prestazioni con più inclusioni

Quando si sentono domande di prestazioni che coinvolgono problemi di tempo di risposta del server, l'origine del problema è spesso query con più istruzione Include. Anche se l'inclusione di entità correlate in una query è potente, è importante comprendere cosa sta succedendo sotto le coperte.

Richiede un tempo relativamente lungo per una query con più Include istruzioni in esso per passare attraverso il compilatore piano interno per produrre il comando di archivio. La maggior parte di questo tempo viene impiegato cercando di ottimizzare la query risultante. Il comando di archivio generato conterrà un Outer Join o un'unione per ogni include, a seconda del mapping. Query come questa porteranno in grandi grafici connessi dal database in un singolo payload, che si acerbate eventuali problemi di larghezza di banda, soprattutto quando c'è un sacco di ridondanza nel payload (ad esempio, quando più livelli di Include vengono utilizzati per attraversare le associazioni nella direzione uno-a-molti).

È possibile verificare la presenza di casi in cui le query restituiscono payload eccessivamente grandi accedendo al TSQL sottostante per la query utilizzando ToTraceString ed eseguendo il comando store in SQL Server Management StudioSQL Server Management Studio per visualizzare le dimensioni del payload. In questi casi è possibile provare a ridurre il numero di istruzioni Include nella query per inserire solo i dati necessari. In alternativa, è possibile suddividere la query in una sequenza più piccola di sottoquery, ad esempio:

**Prima di interrompere la query:**

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

**Dopo aver violato la query:**

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

Questo funzionerà solo sulle query monitorate, come stiamo facendo uso della capacità del contesto ha di eseguire la risoluzione dell'identità e la correzione di associazione automaticamente.

Come per il caricamento lazy, il compromesso sarà più query per payload più piccoli. È anche possibile usare proiezioni di singole proprietà per selezionare in modo esplicito solo i dati necessari da ogni entità, ma in questo caso non verranno caricate entità e gli aggiornamenti non saranno supportati.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Soluzione alternativa per ottenere il caricamento lazy delle proprietà

Entity Framework attualmente non supporta il caricamento lazy di proprietà scalari o complesse. Tuttavia, nei casi in cui si dispone di una tabella che include un oggetto di grandi dimensioni, ad esempio un BLOB, è possibile utilizzare la suddivisione delle tabelle per separare le proprietà di grandi dimensioni in un'entità separata. Si supponga, ad esempio, di disporre di una tabella Product che include una colonna di foto varbinary. Se non è spesso necessario accedere a questa proprietà nelle query, è possibile usare la suddivisione delle tabelle per importare solo le parti dell'entità normalmente necessarie. L'entità che rappresenta la foto del prodotto verrà caricata solo quando ne hai esplicitamente bisogno.

Una buona risorsa che mostra come abilitare la suddivisione delle tabelle è il post \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>di blog "Table Splitting in Entity Framework" di Gil Fink: .

## <a name="9-other-considerations"></a>9 Altre considerazioni

### <a name="91-server-garbage-collection"></a>9.1 Garbage Collection per server

Alcuni utenti potrebbero riscontrare conflitti di risorse che limitano il parallelismo che si aspettano quando il Garbage Collector non è configurato correttamente. Ogni volta che EF viene utilizzato in uno scenario multithreading o in qualsiasi applicazione simile a un sistema lato server, assicurarsi di abilitare Garbage Collection Server. Questa operazione viene eseguita tramite una semplice impostazione nel file di configurazione dell'applicazione:This is done via a simple setting in your application config file:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Questo dovrebbe ridurre la contesa dei thread e aumentare la velocità effettiva fino al 30% in scenari saturi di CPU. In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.

### <a name="92-autodetectchanges"></a>9.2 Rilevamento automaticoModifiche

Come accennato in precedenza, Entity Framework potrebbe mostrare problemi di prestazioni quando la cache degli oggetti ha molte entità. Alcune operazioni, ad esempio Add, Remove, Find, Entry e SaveChanges, attivano le chiamate a DetectChanges che potrebbero utilizzare una grande quantità di CPU in base alle dimensioni della cache degli oggetti. Il motivo è che la cache degli oggetti e il gestore dello stato degli oggetti tentano di rimanere il più sincronizzati possibile in ogni operazione eseguita in un contesto in modo che i dati prodotti siano corretti in un'ampia gamma di scenari.

È in genere consigliabile lasciare abilitato il rilevamento automatico delle modifiche di Entity Framework per l'intera durata dell'applicazione. Se lo scenario è influenzato negativamente dall'utilizzo elevato della CPU e i profili indicano che il colpevole è la chiamata a DetectChanges, è consigliabile disattivare temporaneamente AutoDetectChanges nella parte sensibile del codice:

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

Prima di disattivare AutoDetectChanges, è bene comprendere che ciò potrebbe causare Entity Framework perdere la capacità di tenere traccia di alcune informazioni sulle modifiche che si stanno svolgendo sulle entità. Se gestito in modo non corretto, ciò potrebbe causare l'incoerenza dei dati nell'applicazione. Per ulteriori informazioni sulla disattivazione \< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>di AutoDetectChanges, leggere .

### <a name="93-context-per-request"></a>9.3 Contesto per richiesta

I contesti di Entity Framework sono pensati per essere utilizzati come istanze di breve durata per fornire l'esperienza di prestazioni ottimale. Si prevede che i contesti siano di breve durata ed eliminati e, come tali, sono stati implementati per essere molto leggeri e riutilizzare i metadati quando possibile. Negli scenari Web è importante tenere presente questo aspetto e non disporre di un contesto per più della durata di una singola richiesta. Analogamente, negli scenari non Web, il contesto deve essere eliminato in base alla comprensione dei diversi livelli di memorizzazione nella cache in Entity Framework. In generale, è consigliabile evitare di avere un'istanza di contesto per tutta la durata dell'applicazione, nonché contesti per thread e contesti statici.

### <a name="94-database-null-semantics"></a>9.4 Semantica null del database

Entity Framework per impostazione predefinita\# genererà codice SQL con semantica di confronto null C. Si consideri la query di esempio seguente:

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

In this example, we're comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice. Il codice SQL generato per questa query chiederà se il valore del parametro è uguale al valore della colonna o se entrambi i valori di parametro e colonna sono null. In questo modo si nasconderà il modo in\# cui il server di database gestisce i valori Null e fornirà un'esperienza null C coerente tra diversi fornitori di database. D'altra parte, il codice generato è un po ' contorto e potrebbe non funzionare bene quando la quantità di confronti nell'istruzione where della query aumenta fino a un numero elevato.

Un modo per gestire questa situazione consiste nell'utilizzare la semantica null del database. Si noti che questo potrebbe potenzialmente\# comportarsi in modo diverso per la semantica null C dal momento che ora Entity Framework genererà SQL più semplice che espone il modo in cui il motore di database gestisce i valori null. La semantica null del database può essere attivata per ogni contesto con una singola riga di configurazione per la configurazione del contesto:Database null semantics can be activated per-context with one single configuration line against the context configuration:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Le query di piccole e medie dimensioni non visualizzeranno un miglioramento delle prestazioni percepibile quando si usa la semantica null del database, ma la differenza diventerà più evidente nelle query con un numero elevato di potenziali confronti null.

Nella query di esempio precedente, la differenza di prestazioni era inferiore al 2% in un microbenchmark in esecuzione in un ambiente controllato.

### <a name="95-async"></a>9.5 Asincrono

Entity Framework 6 ha introdotto il supporto delle operazioni asincrone durante l'esecuzione in .NET 4.5 o versioni successive. Per la maggior parte, le applicazioni con conflitti correlati all'I/O trarranno il massimo vantaggio dall'utilizzo di operazioni di query e salvataggio asincrone. Se l'applicazione non soffre di conflitti di I/O, l'uso di async, nei casi migliori, verrà eseguito in modo sincrono e restituirà il risultato nello stesso periodo di tempo di una chiamata sincrona o, nel peggiore dei casi, semplicemente rinviare l'esecuzione a un'attività asincrona e aggiungere più tempo al completamento dello scenario.

Per informazioni sul funzionamento della programmazione asincrona che consente di decidere [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)se async migliorerà le prestazioni dell'applicazione visita . Per ulteriori informazioni sull'utilizzo delle operazioni asincrone in Entity Framework, vedere [Query asincrona e salvataggio](~/ef6/fundamentals/async.md
).

### <a name="96-ngen"></a>9.6 NGEN (Gen)

Entity Framework 6 non viene fornito nell'installazione predefinita di .NET Framework. Di conseguenza, gli assembly di Entity Framework non sono NGEN'd per impostazione predefinita, il che significa che tutto il codice Entity Framework è soggetto agli stessi costi JIT'ing di qualsiasi altro assembly MSIL. Ciò potrebbe ridurre l'esperienza F5 durante lo sviluppo e anche l'avvio a freddo dell'applicazione negli ambienti di produzione. Al fine di ridurre i costi di CPU e memoria di JIT'ing è consigliabile NGEN le immagini di Entity Framework a seconda dei casi. Per ulteriori informazioni su come migliorare le prestazioni di avvio di Entity Framework 6 con NGEN, vedere Miglioramento delle prestazioni di [avvio con NGen](~/ef6/fundamentals/performance/ngen.md).

### <a name="97-code-first-versus-edmx"></a>9.7 Code First rispetto a EDMX

Entity Framework motivi circa il problema di mancata corrispondenza impediti tra la programmazione orientata agli oggetti e database relazionali con una rappresentazione in memoria del modello concettuale (gli oggetti), lo schema di archiviazione (il database) e un mapping tra i due. Questi metadati sono denominati Entity Data Model o EDM in breve. Da questo EDM, Entity Framework deriverà le viste ai dati di andata e ritorno dagli oggetti in memoria al database e viceversa.

Quando Entity Framework viene utilizzato con un file EDMX che specifica formalmente il modello concettuale, lo schema di archiviazione e il mapping, la fase di caricamento del modello deve solo verificare che l'EDM sia corretto (ad esempio, assicurarsi che non manchino mapping), quindi generare le visualizzazioni, quindi convalidare le visualizzazioni e avere questi metadati pronti per l'uso. Solo allora è possibile eseguire una query o salvare nuovi dati nell'archivio dati.

L'approccio Code First è, a suo centro, un sofisticato generatore Entity Data Model. Entity Framework deve produrre un EDM dal codice fornito; lo fa analizzando le classi coinvolte nel modello, applicando le convenzioni e configurando il modello tramite l'API Fluent. Dopo la compilazione dell'EDM, Entity Framework si comporta essenzialmente nello stesso modo in cui presenterebbe un file EDMX nel progetto. Pertanto, la compilazione del modello da Code First aggiunge ulteriore complessità che si traduce in un tempo di avvio più lento per Entity Framework rispetto alla presenza di un EDMX. Il costo dipende completamente dalle dimensioni e dalla complessità del modello in fase di compilazione.

Quando si sceglie di utilizzare EDMX rispetto a Code First, è importante sapere che la flessibilità introdotta da Code First aumenta il costo di compilazione del modello per la prima volta. Se l'applicazione è in grado di sopportare il costo di questo primo caricamento, in genere Code First sarà la strada preferita da percorrere.

## <a name="10-investigating-performance"></a>10 Indagine sulle prestazioni

### <a name="101-using-the-visual-studio-profiler"></a>10.1 Utilizzo di Visual Studio Profiler

Se si verificano problemi di prestazioni con Entity Framework, è possibile usare un profiler simile a quello incorporato in Visual Studio per vedere dove l'applicazione sta trascorrendo il proprio tempo. Questo è lo strumento che abbiamo usato per generare i grafici a torta nel \< https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) post di blog "Esplorazione delle prestazioni del ADO.NET Entity Framework - Parte 1" (che mostrano dove Entity Framework trascorre il suo tempo durante le query fredde e calde.

Il post di blog "Profiling Entity Framework using the Visual Studio 2010 Profiler" scritto dal team di consulenza clienti Data e modellazione mostra un esempio reale di come hanno utilizzato il profiler per analizzare un problema di prestazioni.\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. Questo post è stato scritto per un'applicazione Windows. Se è necessario profilare un'applicazione Web gli strumenti Windows Performance Recorder (WPR) e Windows Performance Analyzer (WPA) potrebbero funzionare meglio che funzionare da Visual Studio. WPR e WPA fanno parte di Windows Performance Toolkit incluso [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)in Windows Assessment and Deployment Kit ( ).

### <a name="102-applicationdatabase-profiling"></a>10.2 Profilazione di applicazioni/database

Strumenti come il profiler integrato in Visual Studio indicano dove l'applicazione sta trascorrendo tempo.È disponibile un altro tipo di profiler che esegue l'analisi dinamica dell'applicazione in esecuzione, in produzione o in pre-produzione a seconda delle esigenze, e cerca le insidie comuni e gli anti-modelli di accesso al database.

Due profiler disponibili in commercio sono \< http://efprof.com>) Entity Framework \< http://ormprofiler.com>)Profiler ( e ORMProfiler ( .

Se l'applicazione è un'applicazione MVC che utilizza Code First, è possibile utilizzare MiniProfiler di StackExchange. Scott Hanselman descrive questo strumento nel \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>suo blog all'indirizzo: .

Per ulteriori informazioni sulla profilatura dell'attività del database dell'applicazione, vedere l'articolo MSDN Magazine di Julie Lerman intitolato [Profiling Database Activity in The Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10.3 Logger di database

Se si utilizza Entity Framework 6 è anche prendere in considerazione l'utilizzo della funzionalità di registrazione incorporata. Il Database proprietà del contesto può essere istruito per registrare la propria attività tramite una semplice configurazione di una riga:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

In questo esempio l'attività del database verrà registrata nella console, ma&lt;&gt; la proprietà Log può essere configurata per chiamare qualsiasi delegato della stringa Di azione.

Se si desidera abilitare la registrazione del database senza ricompilare e si utilizza Entity Framework 6.1 o versione successiva, è possibile farlo aggiungendo un intercettore nel file web.config o app.config dell'applicazione.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Per ulteriori informazioni su come aggiungere la registrazione \< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>senza ricompilare, visitare .

## <a name="11-appendix"></a>11 Appendice

### <a name="111-a-test-environment"></a>11.1 A. Ambiente di prova

Questo ambiente utilizza un'installazione su due computer con il database in un computer separato dall'applicazione client. Le macchine si trovano nello stesso rack, quindi la latenza di rete è relativamente bassa, ma più realistica di un ambiente a computer singolo.

#### <a name="1111-app-server"></a>11.1.1 Server applicazioni

##### <a name="11111-software-environment"></a>11.1.1.1 Ambiente software

-   Ambiente software Entity Framework 4
    -   Nome del sistema operativo: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (solo per alcuni confronti).
-   Ambiente software Entity Framework 5 e 6
    -   Nome del sistema operativo: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>11.1.1.2 Ambiente hardware

-   Processore doppio: CPU Intel(R) Xeon(R) L5520 W3530 - 2,27 GHz, 2261 Mhz8 GHz, 4 Core,84 Processori Logici.
-   2412 GB di RamRAM.
-   Unità SAtA 7200 rpm da 3 GB/s da 136 GB suddivisa in 4 partizioni.

#### <a name="1112-db-server"></a>11.1.2 Server DB

##### <a name="11121-software-environment"></a>11.1.2.1 Ambiente software

-   Nome del sistema operativo: Windows Server 2008 R28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 Ambiente hardware

-   Processore singolo: CPU Intel(R) Xeon(R) L5520 - 2,27 GHz, 2261 MhzES-1620 0 - 3,60 GHz, 4 Core(s), 8 Processori Logici.
-   824 GB di RamRAM.
-   Unità DA 465 GB ATA500 GB SATA 7200 rpm 6GB/s suddivisa in 4 partizioni.

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. Test di confronto delle prestazioni delle query

Il modello Northwind è stato utilizzato per eseguire questi test. È stato generato dal database utilizzando la finestra di progettazione Entity Framework. Quindi, è stato utilizzato il codice seguente per confrontare le prestazioni delle opzioni di esecuzione della query:

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

### <a name="113-c-navision-model"></a>11.3 C. Navision Modello

Il database di Navision è un database di grandi dimensioni utilizzato per la dimostrazione di Microsoft Dynamics – NAV. Il modello concettuale generato contiene 1005 set di entità e 4227 set di associazioni. Il modello utilizzato nel test è "flat" - non è stata aggiunta alcuna ereditarietà.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 Query utilizzate per i test di Navision

L'elenco di query utilizzato con il modello Navision contiene 3 categorie di query Entity SQL:

##### <a name="11311-lookup"></a>11.3.1.1 Ricerca

Una query di ricerca semplice senza aggregazioni

-   Conteggio: 16232Count: 16232
-   Esempio:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Una normale query BI con più aggregazioni, ma senza subtotali (query singola)

-   Conteggio: 2313Count: 2313
-   Esempio:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Dove MDF\_\_SessionLogin Time\_Max() è definito nel modello come:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregazioneSubtotali

Una query BI con aggregazioni e subtotali (tramite Unione tutti)

-   Conteggio: 178Count: 178
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
