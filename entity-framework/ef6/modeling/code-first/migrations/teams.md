---
title: Migrazioni Code First in ambienti Team-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4c2d9a95-de6f-4e97-9738-c1f8043eff69
ms.openlocfilehash: 46c22c997b8db53bb381d0b528c65733f1766849
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103165"
---
# <a name="code-first-migrations-in-team-environments"></a>Migrazioni Code First in ambienti team
> [!NOTE]
> Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base. In caso contrario, sarà necessario leggere [migrazioni Code First](~/ef6/modeling/code-first/migrations/index.md) prima di continuare.

## <a name="grab-a-coffee-you-need-to-read-this-whole-article"></a>Prendi un caffè, devi leggere questo articolo

I problemi negli ambienti Team riguardano principalmente l'Unione delle migrazioni quando due sviluppatori hanno generato migrazioni nella codebase locale. Mentre i passaggi per risolvere questi problemi sono piuttosto semplici, richiedono una conoscenza approfondita del funzionamento delle migrazioni. Non solo andare avanti fino alla fine. leggere l'intero articolo per assicurarsi di avere successo.

## <a name="some-general-guidelines"></a>Alcune linee guida generali

Prima di esaminare come gestire le migrazioni di merge generate da più sviluppatori, di seguito sono riportate alcune linee guida generali per la configurazione del successo.

### <a name="each-team-member-should-have-a-local-development-database"></a>Ogni membro del team deve disporre di un database di sviluppo locale

Le migrazioni utilizzano la ** \_ \_tabella MigrationsHistory** per archiviare quali migrazioni sono state applicate al database. Se si dispone di più sviluppatori che generano migrazioni diverse durante il tentativo di destinazione dello stesso database (e ** \_ \_** di conseguenza condividono una tabella MigrationsHistory), le migrazioni saranno molto confuse.

Naturalmente, se si dispone di membri del team che non generano migrazioni, non si verificano problemi di condivisione di un database di sviluppo centrale.

### <a name="avoid-automatic-migrations"></a>Evitare migrazioni automatiche

Il risultato finale è che le migrazioni automatiche hanno inizialmente un aspetto positivo negli ambienti team, ma in realtà non funzionano. Se si vuole saperlo, continuare a leggere. in caso contrario, è possibile passare alla sezione successiva.

Le migrazioni automatiche consentono di aggiornare lo schema del database in modo che corrisponda al modello corrente senza la necessità di generare file di codice (migrazioni basate su codice). Le migrazioni automatiche funzionano molto bene in un ambiente team se sono state usate solo e non hanno mai generato migrazioni basate sul codice. Il problema è che le migrazioni automatiche sono limitate e non gestiscono una serie di operazioni, ovvero rinominazioni di proprietà/colonne, spostamento di dati in un'altra tabella e così via. Per gestire questi scenari è necessario generare migrazioni basate sul codice (e modificare il codice con impalcature) combinate tra le modifiche gestite da migrazioni automatiche. In questo modo non è più possibile unire le modifiche quando due sviluppatori archiviano le migrazioni.

## <a name="screencasts"></a>Screencast

Se si preferisce guardare uno screencast anziché leggere questo articolo, i due video seguenti riguardano lo stesso contenuto di questo articolo.

### <a name="video-one-migrations---under-the-hood"></a>Video uno: "migrazioni-dietro le quinte"

[Questo screencast](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) illustra il modo in cui le migrazioni tracciano e usano le informazioni sul modello per rilevare le modifiche al modello.

### <a name="video-two-migrations---team-environments"></a>Video due: "migrazioni-ambienti Team"

Basandosi sui concetti del video precedente, lo [screencast](https://channel9.msdn.com/blogs/ef/migrations-team-environments) illustra i problemi che si verificano in un ambiente del team e come risolverli.

## <a name="understanding-how-migrations-works"></a>Informazioni sul funzionamento delle migrazioni

La chiave per utilizzare correttamente le migrazioni in un ambiente team è una conoscenza di base del modo in cui le migrazioni tengono traccia e utilizzano le informazioni sul modello per rilevare le modifiche al modello.

### <a name="the-first-migration"></a>Prima migrazione

Quando si aggiunge la prima migrazione al progetto, è possibile eseguire un'operazione simile a **Add-Migration prima** nella console di gestione pacchetti. Di seguito sono illustrati i passaggi di alto livello eseguiti da questo comando.

![Prima migrazione](~/ef6/media/firstmigration.png)

Il modello corrente viene calcolato dal codice (1). Gli oggetti di database necessari vengono quindi calcolati in base al modello diverso (2): poiché si tratta della prima migrazione, il modello usa solo un modello vuoto per il confronto. Le modifiche necessarie vengono passate al generatore di codice per compilare il codice di migrazione necessario (3) che viene quindi aggiunto alla soluzione Visual Studio (4).

Oltre al codice di migrazione effettivo archiviato nel file di codice principale, le migrazioni generano anche altri file code-behind. Questi file sono metadati usati dalle migrazioni e non sono elementi che è necessario modificare. Uno di questi file è un file di risorse (. resx) che contiene uno snapshot del modello al momento della generazione della migrazione. Si vedrà come verrà usato nel passaggio successivo.

A questo punto è probabile che si esegua **Update-database** per applicare le modifiche al database, quindi si passa all'implementazione di altre aree dell'applicazione.

### <a name="subsequent-migrations"></a>Migrazioni successive

Successivamente, si apportano alcune modifiche al modello. in questo esempio verrà aggiunta una proprietà **URL** a **Blog**. Eseguire quindi un comando, ad esempio **Add-Migration addurl** , per eseguire l'impalcatura di una migrazione per applicare le modifiche corrispondenti al database. Di seguito sono illustrati i passaggi di alto livello eseguiti da questo comando.

![Seconda migrazione](~/ef6/media/secondmigration.png)

Proprio come l'ultima volta, il modello corrente viene calcolato dal codice (1). Tuttavia, questa volta sono presenti migrazioni esistenti, quindi il modello precedente viene recuperato dalla migrazione più recente (2). Questi due modelli sono diffed per individuare le modifiche al database richieste (3) e quindi il processo viene completato come prima.

Questo stesso processo viene usato per tutte le altre migrazioni aggiunte al progetto.

### <a name="why-bother-with-the-model-snapshot"></a>Perché preoccuparsi dello snapshot del modello?

Ci si potrebbe chiedere perché EF bothers con lo snapshot del modello, perché non solo esaminare il database. In caso affermativo, leggere il. Se non si è interessati, è possibile ignorare questa sezione.

Esistono diversi motivi per cui EF mantiene lo snapshot del modello:

-   Consente al database di spostarsi dal modello EF. Queste modifiche possono essere apportate direttamente nel database oppure è possibile modificare il codice con impalcature nelle migrazioni per apportare le modifiche. Di seguito sono riportati alcuni esempi di questa procedura:
    -   Si desidera aggiungere una colonna inserita e aggiornata a una o più tabelle, ma non si desidera includere tali colonne nel modello EF. Se le migrazioni esaminano il database, si tenterà continuamente di eliminare queste colonne ogni volta che si esegue il ponteggi di una migrazione. Utilizzando lo snapshot del modello, EF rileverà solo le modifiche legittime apportate al modello.
    -   Si vuole modificare il corpo di un stored procedure usato per gli aggiornamenti per includere alcune registrazioni. Se le migrazioni esaminavano questa stored procedure dal database, si tenterà continuamente di reimpostarla sulla definizione prevista da EF. Utilizzando lo snapshot del modello, Entity Framework consentirà di modificare solo il codice dell'impalcatura per modificare la stored procedure quando si modifica la forma della stored procedure nel modello EF.
    -   Questi stessi principi si applicano all'aggiunta di indici aggiuntivi, incluse le tabelle aggiuntive nel database, il mapping di EF a una vista di database che si trova su una tabella e così via.
-   Il modello EF contiene più di una sola forma del database. L'uso dell'intero modello consente alle migrazioni di esaminare le informazioni sulle proprietà e le classi del modello e sul modo in cui eseguono il mapping alle colonne e alle tabelle. Queste informazioni consentono alle migrazioni di essere più intelligenti nel codice sottoposto a impalcature. Se ad esempio si modifica il nome della colonna di cui una proprietà esegue il mapping alle migrazioni, è possibile rilevare la ridenominazione visualizzando la stessa proprietà, ovvero un elemento che non può essere eseguito se si dispone solo dello schema del database. 

## <a name="what-causes-issues-in-team-environments"></a>Cosa causa problemi negli ambienti Team

Il flusso di lavoro trattato nella sezione precedente funziona perfettamente quando si è un singolo sviluppatore che lavora su un'applicazione. Funziona anche in un ambiente team se si è l'unico utente che sta apportando modifiche al modello. In questo scenario è possibile apportare modifiche al modello, generare migrazioni e inviarle al controllo del codice sorgente. Gli altri sviluppatori possono sincronizzare le modifiche ed eseguire **Update-database** per applicare le modifiche dello schema.

I problemi iniziano a verificarsi quando si hanno più sviluppatori che apportano modifiche al modello EF e si inviano al controllo del codice sorgente nello stesso momento. Il modo in cui EF manca è un metodo di primo livello per unire le migrazioni locali con le migrazioni inviate da un altro sviluppatore al controllo del codice sorgente dall'ultima sincronizzazione.

## <a name="an-example-of-a-merge-conflict"></a>Esempio di conflitto di merge

Per prima cosa, esaminiamo un esempio concreto di conflitto di merge. Continuiamo con l'esempio esaminato in precedenza. Come punto di partenza si supponga che le modifiche apportate dalla sezione precedente siano state archiviate dallo sviluppatore originale. Verranno monitorati due sviluppatori Man mano che apportano modifiche alla codebase.

Verranno monitorati il modello EF e le migrazioni attraverso alcune modifiche. Per un punto di partenza, entrambi gli sviluppatori sono stati sincronizzati con il repository del controllo del codice sorgente, come illustrato nel grafico seguente.

![Punto di partenza](~/ef6/media/startingpoint.png)

Developer \#1 e Developer \#2 ora apportano alcune modifiche al modello EF nella codebase locale. Developer \#1 aggiunge una proprietà **rating** a **Blog** e genera una migrazione **AddRating** per applicare le modifiche al database. Developer \#2 aggiunge una proprietà **Readers** a **Blog** e genera la migrazione **AddReaders** corrispondente. Entrambi gli sviluppatori eseguono **Update-database**per applicare le modifiche ai database locali e continuare a sviluppare l'applicazione.

> [!NOTE]
> Le migrazioni sono precedute da un timestamp, quindi il grafico indica che la migrazione AddReaders da \#Developer 2 è successiva alla migrazione AddRating da \#Developer 1. Se Developer \#1 o \#2 ha generato la migrazione, non fa alcuna differenza per i problemi relativi all'utilizzo di un team o al processo per l'Unione dei processi che verranno esaminati nella sezione successiva.

![Modifiche locali](~/ef6/media/localchanges.png)

Si tratta di un giorno fortunato per \#Developer 1 Man mano che si verificano prima di inviare le modifiche. Poiché non è stato archiviato alcun altro dato che ha sincronizzato il repository, può semplicemente inviare le modifiche senza eseguire alcuna operazione di merge.

![Submit](~/ef6/media/submit.png)

A questo punto è possibile inviare \#lo sviluppatore 2. Non sono così fortunati. Poiché un altro utente ha inviato modifiche dopo la sincronizzazione, sarà necessario eseguire il pull delle modifiche e unirle. Il sistema di controllo del codice sorgente sarà probabilmente in grado di unire automaticamente le modifiche a livello di codice poiché sono molto semplici. Lo stato del repository \#locale di Developer 2 dopo la sincronizzazione viene illustrato nell'immagine seguente. 

![Pull](~/ef6/media/pull.png)

In questa fase Developer \#2 può eseguire **Update-database** per rilevare la nuova migrazione di **AddRating** (che non è stata applicata al \#database Developer 2) e applicarla. A questo punto, la colonna **rating** viene aggiunta alla tabella **Blogs** e il database è sincronizzato con il modello.

Tuttavia, esistono due problemi:

1.  Anche se **Update-database** applicherà la migrazione **AddRating** , verrà generato un avviso: *Impossibile aggiornare il database in modo che corrisponda al modello corrente perché sono presenti modifiche in sospeso e la migrazione automatica è disabilitata...*
    Il problema è dovuto al fatto che lo snapshot del modello archiviato nell'ultima migrazione (**AddReader**) non dispone della proprietà **rating** nel **Blog** (poiché non faceva parte del modello durante la generazione della migrazione). Code First rileva che il modello nell'ultima migrazione non corrisponde al modello corrente e genera l'avviso.
2.  L'esecuzione dell'applicazione provocherebbe un'eccezione InvalidOperationException indicante che*il modello che supporta il contesto ' BloggingContext ' è stato modificato dopo la creazione del database. Provare a usare Migrazioni Code First per aggiornare il database... "*
    Anche in questo caso, il problema è che lo snapshot del modello archiviato nell'ultima migrazione non corrisponde al modello corrente.
3.  Infine, è prevedibile che l'esecuzione di **Add-migrate** ora generi una migrazione vuota (poiché non sono presenti modifiche da applicare al database). Tuttavia, dal momento che le migrazioni confrontano il modello corrente con quello dell'ultima migrazione (che manca la proprietà **rating** ), in realtà verrà eseguita l'impalcatura di un'altra chiamata **AddColumn** da aggiungere nella colonna **rating** . Naturalmente, questa migrazione ha esito negativo durante **Update-database** perché la colonna **rating** esiste già.

## <a name="resolving-the-merge-conflict"></a>Risoluzione del conflitto di Unione

L'aspetto positivo è che non è troppo difficile gestire manualmente il merge, purché sia possibile comprendere il funzionamento delle migrazioni. Quindi, se si è saltato avanti in questa sezione... è necessario tornare indietro e leggere prima il resto dell'articolo.

Sono disponibili due opzioni, la più semplice consiste nel generare una migrazione vuota con il modello corrente corretto come snapshot. La seconda opzione consiste nell'aggiornare lo snapshot nell'ultima migrazione in modo che lo snapshot del modello sia corretto. La seconda opzione è leggermente più complessa e non può essere usata in ogni scenario, ma è anche più pulita perché non comporta l'aggiunta di una migrazione aggiuntiva.

### <a name="option-1-add-a-blank-merge-migration"></a>Opzione 1: aggiungere una migrazione di tipo "merge" vuota

In questa opzione viene generata una migrazione vuota esclusivamente allo scopo di verificare che la migrazione più recente includa lo snapshot del modello corretto archiviato al suo interno.

Questa opzione può essere utilizzata indipendentemente dall'utente che ha generato l'ultima migrazione. Nell'esempio riportato di seguito, lo sviluppatore \#2 sta prendendo in considerazione l'Unione ed è stato generato l'ultima migrazione. È tuttavia possibile usare questi stessi passaggi se Developer \#1 ha generato l'ultima migrazione. I passaggi si applicano anche se sono presenti più migrazioni, che sono state esaminate due per mantenerle semplici.

Il processo seguente può essere usato per questo approccio, a partire dal momento in cui si è consapevoli di avere modifiche che devono essere sincronizzate dal controllo del codice sorgente.

1.  Verificare che le modifiche al modello in sospeso nella codebase locale siano state scritte in una migrazione. Questo passaggio assicura che non si verifichino modifiche legittime al momento di generare la migrazione vuota.
2.  Sincronizzare con il controllo del codice sorgente.
3.  Eseguire **Update-database** per applicare le nuove migrazioni che altri sviluppatori hanno archiviato.
    **_Nota:_** *se non vengono visualizzati avvisi dal comando Update-database, non ci sono nuove migrazioni da altri sviluppatori e non è necessario eseguire altre operazioni di merge.*
4.  Eseguire **Add-Migration &lt;pick\_a\_Name&gt; – IgnoreChanges** (ad esempio, **Add-migrate merge – IgnoreChanges**). In questo modo viene generata una migrazione con tutti i metadati (incluso uno snapshot del modello corrente), ma verranno ignorate tutte le modifiche rilevate durante il confronto tra il modello corrente e lo snapshot nelle ultime migrazioni (ovvero si ottiene **un metodo vuoto** e **attivo** ).
5.  Eseguire **Update-database** per applicare nuovamente la migrazione più recente con i metadati aggiornati.
6.  Continuare a sviluppare o inviare al controllo del codice sorgente (dopo l'esecuzione degli unit test di corso).

Di seguito è riportato lo stato \#della codebase locale dello sviluppatore 2 dopo aver usato questo approccio.

![Migrazione di tipo merge](~/ef6/media/mergemigration.png)

### <a name="option-2-update-the-model-snapshot-in-the-last-migration"></a>Opzione 2: aggiornare lo snapshot del modello nell'ultima migrazione

Questa opzione è molto simile all'opzione 1, ma rimuove la migrazione vuota aggiuntiva, perché si tratta di un'opzione che richiede file di codice aggiuntivi nella soluzione.

**Questo approccio è possibile solo se la migrazione più recente esiste solo nella codebase locale e non è ancora stata inviata al controllo del codice sorgente (ad esempio, se l'ultima migrazione è stata generata dall'utente che esegue il merge)**. La modifica dei metadati delle migrazioni che altri sviluppatori potrebbero avere già applicato al database di sviluppo, o ancora peggio applicati a un database di produzione, può causare effetti collaterali imprevisti. Durante il processo verrà eseguito il rollback dell'ultima migrazione nel database locale, che verrà nuovamente applicata ai metadati aggiornati.

Mentre l'ultima migrazione deve trovarsi nella codebase locale, non sono previste restrizioni per il numero o l'ordine delle migrazioni che lo procedono. Possono essere presenti più migrazioni da più sviluppatori diversi e si applicano le stesse procedure, che sono state esaminate due per mantenerle semplici.

Il processo seguente può essere usato per questo approccio, a partire dal momento in cui si è consapevoli di avere modifiche che devono essere sincronizzate dal controllo del codice sorgente.

1.  Verificare che le modifiche al modello in sospeso nella codebase locale siano state scritte in una migrazione. Questo passaggio assicura che non si verifichino modifiche legittime al momento di generare la migrazione vuota.
2.  Sincronizzare con il controllo del codice sorgente.
3.  Eseguire **Update-database** per applicare le nuove migrazioni che altri sviluppatori hanno archiviato.
    **_Nota:_** *se non vengono visualizzati avvisi dal comando Update-database, non ci sono nuove migrazioni da altri sviluppatori e non è necessario eseguire altre operazioni di merge.*
4.  Eseguire **Update-database – TargetMigration &lt;seconda\_Ultima\_migrazione&gt; ** (nell'esempio seguente si tratta di **Update-database – TargetMigration AddRating**). Questa operazione consente di riportare il database allo stato della seconda ultima migrazione, ovvero di annullare l'applicazione dell'ultima migrazione dal database.
    **_Nota:_** *questo passaggio è necessario per rendere sicuro la modifica dei metadati della migrazione poiché i metadati vengono archiviati anche in \_ \_MigrationsHistoryTable del database. Questo è il motivo per cui è consigliabile usare questa opzione solo se l'ultima migrazione è solo nella codebase locale. Se per altri database era stata applicata l'ultima migrazione, sarebbe necessario eseguirne il rollback e riapplicare l'ultima migrazione per aggiornare i metadati.* 
5.  Eseguire l'operazione **di &lt;aggiunta\_-\_migrazione\_nome\_completo\_,\_incluso il timestamp dell'ultima migrazione** &gt; (nell'esempio seguente si tratta di una procedura simile a **Add\_-Migration 201311062215252 AddReaders**).
    **_Nota:_** *è necessario includere il timestamp in modo che le migrazioni sappiano che si vuole modificare la migrazione esistente anziché eseguire l'impalcatura di una nuova.*
    In questo modo i metadati per l'ultima migrazione vengono aggiornati in base al modello corrente. Quando il comando viene completato, verrà visualizzato l'avviso seguente, ma è esattamente quello che si desidera. "*Solo il codice della finestra di progettazione per\_la migrazione ' 201311062215252 AddReaders ' è stato nuovamente sottoponte. Per eseguire nuovamente l'impalcatura dell'intera migrazione, usare il parametro-Force ".*
6.  Eseguire **Update-database** per applicare nuovamente la migrazione più recente con i metadati aggiornati.
7.  Continuare a sviluppare o inviare al controllo del codice sorgente (dopo l'esecuzione degli unit test di corso).

Di seguito è riportato lo stato \#della codebase locale dello sviluppatore 2 dopo aver usato questo approccio.

![Metadati aggiornati](~/ef6/media/updatedmetadata.png)

## <a name="summary"></a>Riepilogo

Quando si usa Migrazioni Code First in un ambiente team, si verificano alcuni problemi. Tuttavia, una conoscenza di base del funzionamento delle migrazioni e di alcuni semplici approcci per la risoluzione dei conflitti di merge rendono più semplice superare queste problematiche.

Il problema fondamentale è che i metadati archiviati nella migrazione più recente non sono corretti. In questo modo Code First rilevare erroneamente che lo schema del database e del modello corrente non corrispondono e per eseguire il patibolo di codice errato nella migrazione successiva. Questa situazione può essere superata generando una migrazione vuota con il modello corretto o aggiornando i metadati nella migrazione più recente.
