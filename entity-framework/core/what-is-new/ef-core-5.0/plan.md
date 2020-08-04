---
title: Pianificare Entity Framework Core 5,0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 4abb6f500dce320dd0c32f8f3bf5c529b59fb28b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526888"
---
# <a name="plan-for-entity-framework-core-50"></a>Pianificare Entity Framework Core 5,0

Come descritto nel [processo di pianificazione](xref:core/what-is-new/release_planning), l'input è stato raccolto dagli stakeholder in un piano provvisorio per la versione di EF Core 5,0.

> [!IMPORTANT]
> Questo piano è ancora in fase di elaborazione. Niente qui è un impegno. Questo piano è un punto di partenza che si evolverà Man mano che si apprenderanno altre informazioni. È possibile che venga effettuato il pull di alcuni elementi attualmente non pianificati per 5,0. È possibile che vengano apportate alcune operazioni attualmente pianificate per 5,0.

## <a name="general-information"></a>Informazioni generali

### <a name="version-number-and-release-date"></a>Numero di versione e data di rilascio

EF Core 5,0 è attualmente pianificata per la versione allo [stesso tempo di .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/). È stata scelta la versione "5,0" per l'allineamento con .NET 5,0.

### <a name="supported-platforms"></a>Piattaforme supportate

EF Core 5,0 è pianificato per essere eseguito su qualsiasi piattaforma .NET 5,0 in base alla [convergenza di queste piattaforme con .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/). Ciò significa che in termini di .NET Standard e il TFM effettivo usato è ancora da definire.

EF Core 5,0 non viene eseguito su .NET Framework.

### <a name="breaking-changes"></a>Modifiche di rilievo

EF Core 5,0 conterrà alcune [modifiche di rilievo](xref:core/what-is-new/ef-core-5.0/breaking-changes), ma saranno molto meno gravi rispetto a quanto accadeva per EF Core 3,0. Il nostro obiettivo è consentire l'aggiornamento della maggior parte delle applicazioni senza interruzioni.

Si prevede che verranno apportate alcune modifiche di rilievo per i provider di database, in particolare per quanto riguarda il supporto di TPT. Tuttavia, si prevede che il lavoro per aggiornare un provider per 5,0 sarà inferiore a quello necessario per l'aggiornamento a 3,0.

## <a name="themes"></a>Temi

Sono state estratte alcune aree o temi principali che costituiranno la base per gli investimenti di grandi dimensioni in EF Core 5,0.

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>Mapping molti-a-molti completamente trasparente per convenzione

Sviluppatori leader: @smitpatel , @AndriySvyryd e@lajones

Rilevato da [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)

Dimensioni della maglietta: L

Stato: in corso

Many-to-many è la [funzionalità più richiesta](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~ 506 voti) nel backlog di GitHub.

Il supporto per le relazioni molti-a-molti può essere suddiviso in tre aree principali:

* Ignorare le proprietà di navigazione: coperte dal tema successivo.
* Tipi di entità del contenitore delle proprietà. Questi consentono un tipo CLR standard (ad esempio `Dictionary` ) da usare per le istanze di entità in modo che un tipo CLR esplicito non sia necessario per ogni tipo di entità. Rilevato da [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).
* Sugar per semplificare la configurazione di relazioni molti-a-molti.

Oltre al supporto per l'esplorazione, è ora possibile effettuare il pull di queste altre aree di molti-a-molti in EF Core 5,0 per offrire un'esperienza completa.

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>Proprietà di navigazione molti-a-molti (a. k. a "Ignora navigazione")

Sviluppatori leader: @smitpatel e@AndriySvyryd

Rilevato da [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)

Dimensioni della maglietta: L

Stato: in corso

Come descritto nel primo tema, il supporto molti-a-molti presenta diversi aspetti.
Questo tema rileva in modo specifico l'uso di esplorazioni Skip.
Il blocco più significativo per coloro che desiderano il supporto molti-a-molti non è in grado di utilizzare le relazioni "naturali", senza fare riferimento alla tabella di join, nella logica di business, ad esempio le query.
Il tipo di entità tabella di join può ancora esistere, ma non dovrebbe essere in grado di ottenere la logica di business.

## <a name="table-per-type-tpt-inheritance-mapping"></a>Mapping di ereditarietà tabella per tipo (TPT)

Sviluppatore principale:@AndriySvyryd

Rilevato da [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)

Dimensioni della maglietta: XL

Stato: in corso

Stiamo eseguendo TPT perché si tratta di una funzionalità estremamente richiesta (~ 289 voti, 3 in generale) e perché richiede alcune modifiche di basso livello che si ritiene siano appropriate per la natura fondamentale del piano generale .NET 5. Si prevede che questo provocherà modifiche di rilievo per i provider di database, anche se queste devono essere molto meno gravi rispetto alle modifiche necessarie per 3,0.

## <a name="filtered-include"></a>Inclusione filtrato

Sviluppatore principale:@maumar

Rilevato da [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)

Dimensioni T-Shirt: M

Stato: in corso

L'inclusione filtrata è una funzionalità molto richiesta (~ 376 voti, 2 nel complesso) che non è una grande quantità di lavoro e che riteniamo che lo sblocco o renderà più semplice molti scenari che attualmente richiedono filtri a livello di modello o query più complesse.

## <a name="split-include"></a>Includi suddivisione

Sviluppatore principale:@smitpatel

Rilevato da [#20892](https://github.com/dotnet/efcore/issues/20892)

Dimensioni della maglietta: L

Stato: in corso

EF Core 3,0 ha modificato il comportamento predefinito per creare una singola query SQL per una query LINQ specificata.
Ciò ha causato regressioni di prestazioni elevate per le query che utilizzano Includi per più raccolte.

In EF Core 5,0, viene mantenuto il nuovo comportamento predefinito.
Tuttavia, EF Core 5,0 consentirà la generazione di più query per la raccolta, inclusi i casi in cui la presenza di una singola query causa un peggioramento delle prestazioni.

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>Razionalizzare ToTable, ToQuery, toview, dati da tabelle e così via

Sviluppatori leader: @maumar e@smitpatel

Rilevato da [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)

Dimensioni della maglietta: L

Stato: in corso

Sono stati apportati miglioramenti alle versioni precedenti per il supporto di SQL non elaborato, tipi autochiave e aree correlate. Tuttavia, vi sono Gap e incoerenze nel modo in cui tutto funziona insieme nel suo complesso. L'obiettivo di 5,0 è correggerli e creare un'esperienza ottimale per la definizione, la migrazione e l'utilizzo di tipi diversi di entità e delle query e degli elementi di database associati. Questo può comportare anche aggiornamenti all'API di query compilata.

Si noti che questo elemento può comportare alcune modifiche sostanziali a livello di applicazione, poiché alcune delle funzionalità attualmente disponibili sono troppo permissive, in modo da consentire agli utenti di raggiungere rapidamente i pozzi di errore. È probabile che si stiano bloccando alcune di queste funzionalità insieme a indicazioni sulle operazioni da eseguire.

## <a name="general-query-enhancements"></a>Miglioramenti generali delle query

Sviluppatori leader: @smitpatel e@maumar

Rilevato da [problemi contrassegnati con `area-query` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)

Dimensioni della maglietta: XL

Stato: in corso

Il codice di traduzione query è stato riscritto in maniera estensiva per EF Core 3,0. Il codice di query si trova in genere in uno stato molto più solido a causa di questo problema. Per 5,0 non è prevista la creazione di modifiche di query principali, al di fuori di quelle necessarie per supportare TPT e ignorare le proprietà di navigazione. Tuttavia, è ancora necessario un lavoro significativo per risolvere alcuni debiti tecnici lasciati dalla revisione 3,0. Si prevede inoltre di correggere molti bug e implementare piccoli miglioramenti per migliorare ulteriormente l'esperienza complessiva della query.

## <a name="migrations-and-deployment-experience"></a>Migrazioni ed esperienza di distribuzione

Sviluppatori leader:@bricelam

Rilevato da [#19587](https://github.com/dotnet/efcore/issues/19587)

Dimensioni della maglietta: L

Stato: in corso

Attualmente, molti sviluppatori migrano i database al momento dell'avvio dell'applicazione. Questa operazione è semplice, ma non è consigliata perché:

* Più thread/processi/server possono tentare di eseguire la migrazione simultanea del database
* Le applicazioni potrebbero provare ad accedere allo stato incoerente durante l'operazione
* In genere le autorizzazioni per il database per modificare lo schema non devono essere concesse per l'esecuzione dell'applicazione
* È difficile ripristinare uno stato pulito se si verificano problemi

Si vuole offrire un'esperienza migliore che consenta di eseguire la migrazione del database in fase di distribuzione in modo semplice. Questa operazione dovrebbe:

* Lavorare su Linux, Mac e Windows
* Esperienza ottimale nella riga di comando
* Supportare scenari con i contenitori
* Usare gli strumenti e i flussi di distribuzione reali usati di frequente
* Integrazione in almeno Visual Studio

Il risultato è probabilmente costituito da numerosi miglioramenti di EF Core (ad esempio, migliori migrazioni in SQLite), insieme a linee guida e collaborazioni a lungo termine con altri team per migliorare le esperienze end-to-end che vanno oltre il solo EF.

## <a name="ef-core-platforms-experience"></a>Esperienza delle piattaforme EF Core

Sviluppatori leader: @roji e@bricelam

Rilevato da [#19588](https://github.com/dotnet/efcore/issues/19588)

Dimensioni della maglietta: L

Stato: non avviato

Sono disponibili indicazioni utili per l'uso di EF Core in applicazioni Web tradizionali di tipo MVC. Linee guida per altre piattaforme e modelli di applicazione mancanti o non aggiornati. Per EF Core 5,0, si prevede di analizzare, migliorare e documentare l'esperienza di utilizzo di EF Core con:

* Blazor
* Novell, incluso l'uso della storia AOT/linker
* WinForms/WPF/WinUI e possibilmente altri U.I. frameworks

È probabile che si verifichino molti piccoli miglioramenti in EF Core, insieme a linee guida e collaborazioni a lungo termine con altri team per migliorare le esperienze end-to-end che vanno oltre la semplice EF.

Le aree specifiche da esaminare sono:

* Distribuzione, inclusa l'esperienza di utilizzo degli strumenti EF, ad esempio per le migrazioni
* Modelli di applicazione, inclusi Novell e blazer, e probabilmente altri
* Esperienze di SQLite, tra cui l'esperienza spaziale e le ricompilazioni di tabelle
* Esperienza AOT e collegamento
* Integrazione di diagnostica, inclusi i contatori delle prestazioni

## <a name="performance"></a>Prestazioni

Sviluppatore principale:@roji

Rilevato da [problemi contrassegnati con `area-perf` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)

Dimensioni della maglietta: L

Stato: in corso

Per EF Core, si prevede di migliorare la nostra suite di benchmark delle prestazioni e di apportare miglioramenti al runtime. Si prevede inoltre di completare la nuova API batch ADO.NET, che è stata prototipata durante il ciclo di rilascio 3,0. Inoltre, a livello di ADO.NET, vengono pianificati miglioramenti delle prestazioni aggiuntivi per il provider npgsql.

Nell'ambito di questo lavoro si prevede anche di aggiungere i contatori delle prestazioni di ADO.NET/EF core e altri dati di diagnostica in base alle esigenze.

## <a name="architecturalcontributor-documentation"></a>Documentazione dell'architettura/collaboratore

Documento principale:@ajcvickers

Rilevato da [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

Dimensioni della maglietta: L

Stato: taglia

L'idea è quella di semplificare la comprensione di ciò che accade negli elementi interni del EF Core. Questa operazione può essere utile per chiunque usi EF Core, ma la motivazione principale consiste nel rendere più semplice per gli utenti esterni:

* Contribuisci al codice EF Core
* Creare provider di database
* Compila altre estensioni

Aggiornamento: Sfortunatamente, questo piano era troppo ambizioso.
Si ritiene ancora che questo aspetto è importante, ma sfortunatamente non verrà atterrato con EF Core 5,0.

## <a name="microsoftdatasqlite-documentation"></a>Documentazione di Microsoft. Data. sqlite

Documento principale:@bricelam

Rilevato da [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

Dimensioni T-Shirt: M

Stato: completato. La nuova documentazione è disponibile nel [sito Microsoft docs](/dotnet/standard/data/sqlite/?tabs=netcore-cli).

Il team EF possiede anche il provider Microsoft. Data. sqlite ADO.NET. Si prevede di documentare completamente questo provider nell'ambito della versione 5,0.

## <a name="general-documentation"></a>Documentazione generale

Documento principale:@ajcvickers

Rilevato da [problemi nel repository docs nell'attività cardine 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)

Dimensioni della maglietta: L

Stato: in corso

È già in corso l'aggiornamento della documentazione per le versioni 3,0 e 3,1. Stiamo lavorando anche:

* Revisione dei documenti introduttivi per renderli più accessibili o più facili da seguire
* Riorganizzazione dei documenti per semplificare la ricerca e l'aggiunta di riferimenti incrociati
* Aggiunta di dettagli e chiarimenti ai documenti esistenti
* Aggiornamento degli esempi e aggiunta di altri esempi

## <a name="fixing-bugs"></a>Correzione dei bug

Rilevato da [problemi contrassegnati con `type-bug` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)

Sviluppatori: @roji , @maumar , @bricelam , @smitpatel , @AndriySvyryd ,@ajcvickers

Dimensioni della maglietta: L

Stato: in corso

Al momento della stesura di questo articolo, sono stati risolti 135 bug nella versione 5,0 (con 62 già corretti), ma si è verificata una sovrapposizione significativa con la sezione relativa ai _miglioramenti generali delle query_ precedente.

La velocità in ingresso (problemi che finiscono come lavoro in un'attività cardine) è stata di circa 23 problemi al mese nel corso della versione 3,0. Non tutti questi dovranno essere corretti in 5,0. Come stima approssimativa si prevede di correggere altri 150 problemi nell'intervallo di tempo 5,0.

## <a name="small-enhancements"></a>Piccoli miglioramenti

Rilevato da [problemi contrassegnati con `type-enhancement` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)

Sviluppatori: @roji , @maumar , @bricelam , @smitpatel , @AndriySvyryd ,@ajcvickers

Dimensioni della maglietta: L

Stato: in corso

Oltre alle funzionalità più importanti descritte in precedenza, sono stati riportati anche numerosi miglioramenti più piccoli pianificati per 5,0 per correggere i "tagli di carta". Si noti che molti di questi miglioramenti sono trattati anche dai temi più generali illustrati in precedenza.

## <a name="below-the-line"></a>Sotto la riga

Rilevato da [problemi con `consider-for-next-release` etichetta](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

Si tratta di correzioni di bug e miglioramenti che **non** sono attualmente pianificati per la versione 5,0, ma verranno esaminati come obiettivi di estensione a seconda dello stato di avanzamento del lavoro precedente.

Inoltre, consideriamo sempre i [problemi più votati](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) durante la pianificazione. Il taglio di uno qualsiasi di questi problemi da un rilascio è sempre penoso, ma è necessario un piano realistico per le risorse disponibili.

## <a name="suggestions"></a>Suggerimenti

I commenti e i suggerimenti dei clienti sulla pianificazione sono importanti. Il modo migliore per indicare l'importanza di un problema consiste nel votare (Pollice in su) per tale problema in GitHub. Questi dati vengono quindi inseriti nel [processo di pianificazione](xref:core/what-is-new/release_planning) per la versione successiva.
