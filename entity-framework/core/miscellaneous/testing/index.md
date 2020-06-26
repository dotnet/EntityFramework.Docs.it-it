---
title: Test del codice che usa EF Core - EF Core
description: Approcci diversi al test delle applicazioni che usano EF Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 71222f17ac4cc1b71052a2e7e916ebcedd2ae0f4
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370539"
---
# <a name="testing-code-that-uses-ef-core"></a>Test del codice che usa EF Core

Il test del codice che accede a un database richiede:
* Esecuzione di query e aggiornamenti sullo stesso sistema di database usato nell'ambiente di produzione.
* Esecuzione di query e aggiornamenti su altri sistemi di database più facili da gestire.
* Usare duplicati di test o un altro meccanismo per evitare del tutto di usare un database.

In questo documento vengono illustrati i pro e contro di ognuna di queste scelte e viene illustrato il modo in cui è possibile usare EF Core con ogni approccio.  

> [!TIP]
> Per esaminare un esempio di codice che illustra i concetti introdotti in questo articolo, vedere [Esempio di test di EF Core](xref:core/miscellaneous/testing/testing-sample). 

## <a name="all-database-providers-are-not-equal"></a>Non tutti i provider di database sono uguali

È molto importante comprendere che EF Core non è progettato per astrarre ogni aspetto del sistema di database sottostante.
EF Core è invece un set comune di modelli e concetti che può essere usato con qualsiasi sistema di database.
I provider di database EF Core sovrappongono quindi il comportamento e le funzionalità specifici del database su questo framework comune.
Questo consente a ogni sistema di database di eseguire le funzionalità peculiari, mantenendo comunque i punti in comune, laddove appropriato, con altri sistemi di database. 

Fondamentalmente, questo significa che cambiare provider di database cambierà il comportamento di EF Core e non ci può aspettare che l'applicazione funzioni correttamente a meno che non si tenga conto in modo esplicito delle eventuali differenze a livello di comportamento.
Detto questo, in molti casi questo cambiamento non creerà problemi perché esiste un elevato livello di elementi in comune tra i database relazionali.
Questo aspetto è sia positivo che negativo.
Positivo perché spostarsi tra sistemi di database diversi può essere relativamente semplice.
Negativo perché può dare un falso senso di sicurezza se l'applicazione non è stata completamente testata per il nuovo sistema di database.  

## <a name="approach-1-production-database-system"></a>Approccio 1: Sistema di database di produzione

Come descritto nella sezione precedente, l'unico modo per assicurarsi di testare l'esecuzione in produzione consiste nell'usare lo stesso sistema di database.
Ad esempio, se l'applicazione distribuita usa SQL Azure, anche i test devono essere eseguiti su SQL Azure.

Il processo sarebbe tuttavia lento e oneroso, se ogni sviluppatore eseguisse i test su SQL Azure lavorando attivamente al codice in contemporanea.
Questo è in effetti il compromesso principale da raggiungere per questi approcci: quando è opportuno deviare dal sistema di database di produzione per migliorare l'efficienza dei test?

Fortunatamente, in questo caso la risposta è piuttosto semplice: usare SQL Server in locale per i test degli sviluppatori.
SQL Azure e SQL Server sono molto simili, quindi il test su SQL Server è in genere un compromesso ragionevole.
Detto questo, è comunque saggio eseguire i test su SQL Azure stesso prima di passare all'ambiente di produzione.
 
### <a name="localdb"></a>LocalDB 

Tutti i principali sistemi di database hanno una qualche forma di "edizione per sviluppatori" per i test locali.
SQL Server include anche una funzionalità denominata [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).
Il vantaggio principale di LocalDB è che l'istanza del database viene attivata su richiesta.
In questo modo si evita l'esecuzione di un servizio di database nel computer anche quando non vengono eseguiti test.

LocalDB non è privo di problemi:
* Non supporta tutte le funzionalità offerte da [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15).
* Non è disponibile in Linux.
* Può causare ritardi durante la prima esecuzione dei test mentre il servizio viene attivato.

Personalmente, non ho mai considerato un problema avere un servizio di database in esecuzione nel computer di sviluppo e in genere consiglio di usare Developer Edition.
Tuttavia, LocalDB potrebbe non essere appropriato per alcuni utenti, soprattutto con computer di sviluppo meno potenti.

L'esecuzione di SQL Server (o qualsiasi altro sistema di database) in un contenitore Docker (o un contenitore simile) è un altro modo per evitare di eseguire il sistema di database direttamente nel computer di sviluppo.  

## <a name="approach-2-sqlite"></a>Approccio 2: SQLite

EF Core verifica principalmente il provider di SQL Server eseguendolo in un'istanza di SQL Server locale.
Questi test eseguono decine di migliaia di query in un paio di minuti in un computer veloce.
Questo dimostra che l'uso del sistema di database reale può essere una soluzione efficiente.
È un mito che l'uso di un database più leggero sia l'unico modo per eseguire rapidamente i test.

Detto questo, cosa accade se per qualsiasi motivo non è possibile eseguire test in un ambiente simile al sistema di database di produzione?
La scelta migliore successiva consiste nell'usare qualcosa con funzionalità simili.
Questo significa in genere un altro database relazionale, quindi [SQLite](https://sqlite.org/index.html) rappresenta la scelta più ovvia.

SQLite è una scelta ottimale perché:
* Viene eseguito in-process con l'applicazione e pertanto presenta un sovraccarico basso.
* Usa file semplici creati automaticamente per i database e quindi non richiede la gestione del database.
* Offre una modalità in memoria che consente di evitare persino la creazione di file.

Tuttavia, tenere presente quanto segue:
* SQLite inevitabilmente non supporta tutte le funzionalità del sistema di database di produzione.
* SQLite si comporterà in modo diverso rispetto al sistema di database di produzione per alcune query.

Quindi, se si usa SQLite per alcuni test, assicurarsi di eseguire test anche sul sistema di database reale.

Vedere [Test con SQLite](xref:core/miscellaneous/testing/sqlite) per indicazioni specifiche per EF Core. 

## <a name="approach-3-the-ef-core-in-memory-database"></a>Approccio 3: Database in memoria di EF Core

EF Core viene fornito con un database in memoria usato per i test interni di EF Core stesso.
Questo database è in genere **non adatto per il test di applicazioni che utilizzano EF Core**. In particolare:

* Non è un database relazionale.
* Non supporta le transazioni.
* Non è possibile eseguire query SQL non elaborate.
* Non è ottimizzato per le prestazioni.

Nessuno di questi aspetti è di particolare importanza quando si eseguono test dei meccanismi interni di EF Core, perché questo approccio viene usato in modo specifico nei casi in cui il database non è rilevante per i test.
D'altra parte, questi aspetti tendono a essere molto importanti per il test di un'applicazione che usa EF Core.

## <a name="unit-testing"></a>Unit test

Si supponga di dover testare una parte della logica di business che potrebbe dover usare alcuni dati di un database, senza dover testare in modo intrinseco le interazioni del database.
Un'opzione consiste nell'usare un [duplicato di test](https://en.wikipedia.org/wiki/Test_double), ad esempio una simulazione o uno scenario fittizio.

I duplicati di test vengono usati a Microsoft per i test interni di EF Core.
Tuttavia, non viene mai tentata la simulazione di DbContext o IQueryable,
perché si tratta di un'operazione difficile, scomoda e fragile.
**Evitare di farlo.**

Per i testing unità che usano DbContext viene invece usato il database in memoria EF.
In questo caso, l'uso del database in memoria EF è appropriato perché il test non dipende dal comportamento del database.
Semplicemente evitare questo approccio per testare le query o gli aggiornamenti di database effettivi.   

In [Esempio di test di EF Core](xref:core/miscellaneous/testing/testing-sample) vengono presentati alcuni test che usano il database in memoria EF, nonché SQL Server e SQLite. 
