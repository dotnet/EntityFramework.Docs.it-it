---
title: Pianificazione della versione EF Core
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning
ms.openlocfilehash: df933ac2462fcc18c53f49d862836fd2d6a4dd99
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370188"
---
# <a name="release-planning-process"></a>Processo di pianificazione delle versioni

Viene chiesto spesso in che modo vengano scelte le funzionalità da inserire in una versione specifica.
In questo documento viene illustrato il processo utilizzato.
Il processo è in continua evoluzione quando si trovano modi migliori per pianificare, ma le idee generali restano invariate.

## <a name="different-kinds-of-releases"></a>Tipi diversi di versioni

Tipi diversi di versione contengono tipi diversi di modifiche.
Ciò significa a sua volta che la pianificazione del rilascio è diversa per diversi tipi di rilascio.

### <a name="patch-releases"></a>Versioni patch

Le versioni patch cambiano solo la parte "patch" della versione.
Ad esempio, EF Core 3,1. **1** è una versione che segnala i problemi rilevati nella EF Core 3,1. **0**.

Le versioni patch hanno lo scopo di correggere i bug critici dei clienti.
Ciò significa che le versioni di patch non includono nuove funzionalità.
Le modifiche all'API non sono consentite nelle versioni di patch, tranne che in circostanze particolari.

La barra per apportare una modifica in una versione patch è molto elevata.
Questo perché è fondamentale che le versioni di patch non introducano nuovi bug.
Pertanto, il processo decisionale enfatizza un valore elevato e un rischio basso.

È più probabile che venga applicata una patch a un problema se:
  * Che ha un effetto su più clienti
  * Si tratta di una regressione da una versione precedente
  * L'errore causa il danneggiamento dei dati

È meno probabile che venga applicata una patch a un problema se:
  * Esistono soluzioni alternative ragionevoli
  * La correzione ha un rischio elevato di interruzioni di altro
  * Il bug è in un caso d'angolo

Questa barra passa gradualmente attraverso la durata di una versione [LTS (Long-Term Support)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . Questo è dovuto al fatto che le versioni LTS enfatizzano la stabilità.

La decisione finale relativa alla patch o meno di un problema viene eseguita dai direttori .NET di Microsoft.

### <a name="minor-releases"></a>Versioni secondarie

Le versioni secondarie cambiano solo la parte "secondaria" della versione.
Ad esempio, EF Core 3. **1**. 0 è una versione che migliora EF Core 3. **0**. 0.

Versioni secondarie:
* Sono progettate per migliorare la qualità e le funzionalità della versione precedente
* Contengono in genere correzioni di bug e nuove funzionalità
* Non includere modifiche di rilievo intenzionali
* Eseguire il push di alcune anteprime preliminari in NuGet

### <a name="major-releases"></a>Versioni principali

Le versioni principali modificano il numero di versione "principale" di EF.
Ad esempio, EF Core **3**. 0,0 è una versione principale che fa un grande passo avanti rispetto EF Core 2.2. x.

Versioni principali:
* Sono progettate per migliorare la qualità e le funzionalità della versione precedente
* Contengono in genere correzioni di bug e nuove funzionalità
  * Alcune delle nuove funzionalità possono essere modifiche fondamentali al modo in cui EF Core funziona
* In genere includono modifiche di rilievo intenzionali
  * Le modifiche di rilievo sono parte integrante dell'evolversi del EF Core durante l'apprendimento
  * Tuttavia, pensiamo con molta attenzione per apportare modifiche di rilievo a causa dell'impatto potenziale del cliente. Potrebbe essere stato troppo aggressivo con le modifiche di rilievo nel passato. In futuro, si impegnerà a ridurre al minimo le modifiche che interrompono le applicazioni e per ridurre le modifiche che interrompono i provider di database e le estensioni.
* Eseguire il push di molte anteprime preliminari in NuGet

## <a name="planning-for-majorminor-releases"></a>Pianificazione per versioni principali/secondarie

### <a name="github-issue-tracking"></a>Rilevamento dei problemi di GitHub

GitHub ( [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) ) è l'origine della verità per tutte le pianificazioni di EF core.

Problemi in GitHub:

* Uno stato
  * I problemi [aperti](https://github.com/dotnet/efcore/issues) non sono stati risolti.
  * Sono stati risolti i problemi [chiusi](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) .
    * Tutti i problemi risolti sono [contrassegnati con la correzione chiusa](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed). Un problema contrassegnato con closed fixed è fisso e Unito, ma potrebbe non essere stato rilasciato.
    * Altre `closed-` etichette indicano altri motivi per la chiusura di un problema. Ad esempio, i duplicati vengono contrassegnati con il duplicato closed.
* Tipo
  * I [bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) rappresentano i bug.
  * I [miglioramenti](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) rappresentano nuove funzionalità o funzionalità migliori nelle funzionalità esistenti.
* Attività cardine
  * I [problemi con nessuna attività cardine](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone) sono considerati dal team. La decisione su come procedere con il problema non è stata ancora apportata o è stata presa in considerazione una modifica alla decisione.
  * I [problemi nell'attività cardine del backlog](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) sono elementi che il team EF considererà di lavorare in una versione futura
    * I problemi del backlog possono essere [contrassegnati con la dicitura-per-next-release](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) che indica che questo elemento di lavoro è elevato nell'elenco per la versione successiva.
  * I problemi aperti in un'attività cardine con versione sono gli elementi su cui il team prevede di lavorare in tale versione. Ad esempio, [questi sono i problemi per cui si prevede di lavorare per EF Core 5,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0).
  * I problemi chiusi in un'attività cardine con versione sono problemi completati per tale versione. Si noti che la versione potrebbe non essere ancora stata rilasciata. Ad esempio, [questi sono i problemi completati per EF Core 3,0](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed).
* Voti!
  * Il voto è il modo migliore per indicare che un problema è importante.
  * Per votare, è sufficiente aggiungere un "pollice" 👍 al problema. Ad esempio, [questi sono i principali problemi votati](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * Inserire anche un commento che descrive i motivi specifici per cui è necessaria la funzionalità se si ritiene che questo valore venga aggiunto. L'aggiunta di commenti a "+ 1" o simile non comporta l'aggiunta di un valore.

### <a name="the-planning-process"></a>Processo di pianificazione

Il processo di pianificazione è più impegnativo rispetto alla semplice esecuzione delle funzionalità richieste in primo piano dal backlog.
Questo è dovuto al fatto che i commenti e i suggerimenti vengono raccolti da più parti interessate in diversi modi.
Viene quindi modellata una versione in base a:

* Input dai clienti
* Input da altri stakeholder
* Direzione strategica
* Risorse disponibili
* Pianifica

Ecco alcune delle domande poste:

1. **Quanti sviluppatori si pensa che useranno la funzionalità e quanto questa migliorerà le loro applicazioni o la loro esperienza?** Per rispondere a questa domanda vengono raccolti commenti e suggerimenti provenienti da numerose fonti, una delle quali sono i commenti e i voti per i problemi. Gli impegni specifici con i clienti importanti sono altri.

2. **Quali sono le soluzioni alternative utilizzabili finché questa funzionalità non viene implementata?** Molti sviluppatori, ad esempio, possono eseguire il mapping di una tabella di join per ovviare alla mancanza del supporto molti-a-molti nativo. Ovviamente, non tutti gli sviluppatori vogliono farlo, ma molti possono e ciò è un fattore di cui tenere conto per la decisione.

3. **L'implementazione di questa funzionalità consente un'evoluzione dell'architettura di EF Core tale da facilitare l'implementazione di altre funzionalità?** Vengono tendenzialmente favorite le funzionalità che rappresentano la base per altre funzionalità. Ad esempio, le entità contenitore di proprietà possono essere utili per procedere verso l'implementazione del supporto molti-a-molti e i costruttori di entità hanno reso possibile il supporto del caricamento lazy.

4. **La funzionalità rappresenta un punto di estensibilità?** Vengono tendenzialmente favoriti i punti di estendibilità rispetto alle normali funzionalità, perché consentono agli sviluppatori di collegare i propri comportamenti e di compensare eventuali funzionalità mancanti.

5. **Qual è la sinergia della funzionalità quando viene usata in combinazione con altri prodotti?** Vengono favorite le funzionalità che consentono l'uso o che migliorano significativamente l'esperienza d'uso di EF Core con altri prodotti, ad esempio .NET Core, la versione più recente di Visual Studio, Microsoft Azure e così via.

6. **Quali sono le competenze delle persone disponibili per lavorare a una funzionalità e in che modo è possibile sfruttare al meglio queste risorse?** Ogni membro del team EF e i collaboratori della community hanno diversi livelli di esperienza in aree diverse ed è quindi necessario pianificare di conseguenza. Anche se si volesse usufruire della collaborazione di tutti su una funzionalità specifica, ad esempio le traslazioni GroupBy o il supporto molti-a-molti, questo non sarebbe pratico.
