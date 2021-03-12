---
title: Pianificare Entity Framework Core 6,0
description: I temi e le funzionalità pianificati per EF Core 6,0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: d6d4f957a57d8ab6a11232b66a5b2a1238df59a3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023484"
---
# <a name="plan-for-entity-framework-core-60"></a>Pianificare Entity Framework Core 6,0

Come descritto nel [processo di pianificazione](xref:core/what-is-new/release-planning), l'input è stato raccolto dagli stakeholder in un piano per la versione di Entity Framework Core (EF Core) 6,0.

Diversamente dalle versioni precedenti, questo piano non tenta di coprire tutto il lavoro per la versione 6,0. Indica invece dove e come intendiamo investire in questa versione, ma con la flessibilità necessaria per regolare l'ambito o effettuare il pull di nuove attività quando si raccolgono commenti e suggerimenti e si impara a lavorare al rilascio.

> [!IMPORTANT]
> Questo piano non è un impegno. Si tratta di un punto di partenza che si evolverà Man mano che si apprenderanno altre informazioni. È possibile che venga effettuato il pull di alcuni elementi attualmente non pianificati per 6,0. È possibile che vengano apportate alcune operazioni attualmente pianificate per 6,0.

## <a name="general-information"></a>Informazioni generali

### <a name="version-number-and-release-date"></a>Numero di versione e data di rilascio

EF Core 6,0 è la versione successiva dopo EF Core 5,0 ed è attualmente pianificata per il rilascio nel 2021 novembre allo stesso tempo di .NET 6.

### <a name="supported-platforms"></a>Piattaforme supportate

EF Core 6,0 attualmente è destinato a .NET 5. Questa operazione sarà probabilmente aggiornata a .NET 6 come in prossimità della versione. EF Core 6,0 non è destinata ad alcuna versione di .NET Standard; Per ulteriori informazioni, vedere [il futuro del .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

EF Core 6,0 non viene eseguito su .NET Framework.

EF Core 6,0 si allineerà con .NET 6 come [versione di supporto a lungo termine (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="breaking-changes"></a>Modifiche che causano un'interruzione

EF Core 6,0 conterrà un numero ridotto di [modifiche di rilievo](xref:core/what-is-new/ef-core-6.0/breaking-changes) Man mano che si continua a evolvere sia EF core che la piattaforma .NET. Il nostro obiettivo è consentire l'aggiornamento della maggior parte delle applicazioni senza interruzioni.

## <a name="themes"></a>Temi

Le aree seguenti costituiscono la base per gli investimenti di grandi dimensioni in EF Core 6,0.

## <a name="highly-requested-features"></a>Funzionalità altamente richieste

Come sempre, un input principale nel [processo di pianificazione](xref:core/what-is-new/release-planning) deriva dal [Voto ( 👍 ) per le funzionalità di GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc). Per EF Core 6,0 si prevede di utilizzare le seguenti funzionalità altamente richieste:

### <a name="sql-server-temporal-tables"></a>SQL Server tabelle temporali

Rilevato da [#4693](https://github.com/dotnet/efcore/issues/4693)

Stato: non avviato

Dimensioni T-Shirt: grandi

Le tabelle temporali supportano le query per i dati archiviati nella tabella in _qualsiasi momento_, anziché solo i dati più recenti archiviati come nel caso delle tabelle normali. EF Core 6,0 consentirà la creazione di tabelle temporali tramite migrazioni, oltre a consentire l'accesso ai dati tramite query LINQ.

Questo lavoro viene inizialmente definito come [descritto nel problema](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974). È possibile ottenere supporto aggiuntivo in base ai commenti e suggerimenti durante la versione.

### <a name="json-columns"></a>Colonne JSON

Rilevato da [#4021](https://github.com/dotnet/efcore/issues/4021)

Stato: non avviato

Dimensioni T-Shirt: medie

Questa funzionalità introdurrà un meccanismo comune e i modelli per il supporto JSON che possono essere implementati da qualsiasi provider di database. Si collaborerà con la community per allineare le implementazioni esistenti per [npgsql](https://github.com/npgsql/efcore.pg) e [pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), oltre a aggiungere il supporto per SQL Server e SQLite.

### <a name="columnattributeorder"></a>ColumnAttribute. Order

Rilevato da [#10059](https://github.com/dotnet/efcore/issues/10059)

Stato: non avviato

Dimensioni della maglietta: Small

Questa funzionalità consente l'ordinamento arbitrario delle colonne durante **la creazione di una tabella** con migrazioni o `EnsureCreated` . Si noti che per modificare l'ordine delle colonne in una tabella esistente, è necessario ricompilare la tabella e non si tratta di un elemento che si prevede di supportare in qualsiasi EF Core versione.

## <a name="performance"></a>Prestazioni

Sebbene EF Core sia in genere più veloce di EF6, sono ancora presenti aree in cui è possibile ottenere miglioramenti significativi delle prestazioni. Si prevede di affrontare diverse di queste aree in EF Core 6,0, migliorando al tempo stesso l'infrastruttura e i test delle prestazioni.

Questo tema implica una grande indagine iterativa, che informa sulla posizione in cui vengono concentrate le risorse. Si prevede di iniziare con:

### <a name="performance-infrastructure-and-new-tests"></a>Infrastruttura di prestazioni e nuovi test

Stato: non avviato

Dimensioni T-Shirt: medie

Il EF Core codebase contiene già un set di benchmark delle prestazioni che vengono eseguiti ogni giorno. Per 6,0, si prevede di migliorare l'infrastruttura per questi test, nonché di aggiungere nuovi test. Si profilano anche gli scenari di prestazioni principali e si correggeranno tutti i frutti a bassa impiccagione.

### <a name="compiled-models"></a>Modelli compilati

Rilevato da [#1906](https://github.com/dotnet/efcore/issues/1906)

Stato: non avviato

Dimensioni T-Shirt: X-Large

I modelli compilati consentiranno la generazione di una forma compilata del modello EF. In questo modo si otterranno prestazioni di avvio migliori, oltre a prestazioni generalmente migliori durante l'accesso al modello.

### <a name="techempower-fortunes"></a>Fortune TechEmpower

Rilevato da [#23611](https://github.com/dotnet/efcore/issues/23611)

Stato: non avviato

Dimensioni T-Shirt: X-Large

Sono stati eseguiti i [benchmark TechEmpower](https://www.techempower.com/benchmarks/) standard di settore su .NET rispetto a un database PostgreSQL per diversi anni. Il [benchmark Fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) è particolarmente rilevante per gli scenari EF. Sono disponibili più varianti di questo benchmark, tra cui:

- Implementazione di che utilizza direttamente ADO.NET. Questa è l'implementazione più rapida dei tre elencati di seguito.
- Implementazione di che utilizza l'oggetto [elegante](https://www.nuget.org/packages/Dapper/). Si tratta di un'operazione più lenta rispetto all'uso diretto di ADO.NET, ma sempre veloce.
- Implementazione di che utilizza EF Core. Si tratta attualmente dell'implementazione più lenta dei tre.

L'obiettivo di EF Core 6,0 è ottenere le prestazioni di EF Core in modo che corrispondano a quelle di elegante nel benchmark TechEmpower fortunes. Si tratta di una sfida significativa, ma la nostra soluzione migliore è quella più adatta possibile.

### <a name="linkeraot"></a>Linker/AOT

Rilevato da [#10963](https://github.com/dotnet/efcore/issues/10963)

Stato: non avviato

Dimensioni T-Shirt: medie

EF Core esegue grandi quantità di generazione di codice in fase di esecuzione. Si tratta di un'operazione complessa per i modelli di app che dipendono dall'agitazione dell'albero del linker, ad esempio Novell e blazer, e dalle piattaforme che non consentono la compilazione dinamica, ad esempio iOS. Si continuerà a esaminare questo spazio come parte del EF Core 6,0 e si apporteranno miglioramenti mirati come possiamo. Tuttavia, non è previsto che il gap venga chiuso completamente nell'intervallo di tempo 6,0.

## <a name="migrations-and-deployment"></a>Migrazioni e distribuzione

In seguito alle [indagini eseguite per la EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), si prevede di introdurre un supporto migliorato per la gestione delle migrazioni e la distribuzione di database. Sono incluse due aree principali:

### <a name="migrations-bundles"></a>Bundle di migrazioni

Rilevato da [#19693](https://github.com/dotnet/efcore/issues/19693)

Stato: non avviato

Dimensioni T-Shirt: medie

Un bundle di migrazioni è un eseguibile indipendente che applica le migrazioni a un database di produzione. Il comportamento corrisponderà `dotnet ef database update` , ma dovrebbe rendere molto più semplice la distribuzione SSH/Docker/PowerShell, poiché tutto il necessario è contenuto nel singolo eseguibile.

### <a name="managing-migrations"></a>Gestione delle migrazioni

Rilevato da [#22945](https://github.com/dotnet/efcore/issues/22945)

Stato: non avviato

Dimensioni T-Shirt: grandi

Il numero di migrazioni create per un'applicazione può aumentare fino a diventare un carico. Inoltre, queste migrazioni vengono spesso distribuite con l'applicazione anche quando questo non è necessario. In EF Core 6,0, si prevede di migliorare questo problema grazie a strumenti e gestione di progetti/assembly migliori. Due problemi specifici che si intende affrontare sono la [squash di numerose migrazioni in uno](https://github.com/dotnet/efcore/issues/2174) e [la rigenerazione di uno snapshot del modello pulito](https://github.com/dotnet/efcore/issues/18557).

## <a name="improve-existing-features-and-fix-bugs"></a>Migliorare le funzionalità esistenti e correggere i bug

Qualsiasi [problema o bug assegnato all'attività cardine 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) è attualmente pianificato per questa versione. Sono inclusi numerosi piccoli miglioramenti e correzioni di bug.

### <a name="ef6-query-parity"></a>Parità di query EF6

Rilevato da [problemi con etichetta "EF6-parità" e nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)

Stato: non avviato

Dimensioni T-Shirt: grandi

EF Core 5,0 supporta la maggior parte dei modelli di query supportati da EF6, oltre ai modelli non supportati in EF6. Per EF Core 6,0, si prevede di chiudere il gap e di rendere supportato EF Core le query su un vero superset di query EF6 supportate. Questa operazione sarà determinata dall'analisi dei Gap, ma include già problemi di GroupBy, ad esempio [translate GroupBy seguito da FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), e query SQL non elaborate per i tipi [primitivi](https://github.com/dotnet/efcore/issues/11624) e non [mappati](https://github.com/dotnet/efcore/issues/10753) .  

### <a name="value-objects"></a>Oggetti valore

Rilevato da [#9906](https://github.com/dotnet/efcore/issues/9906)

Stato: non avviato

Dimensioni T-Shirt: medie

In precedenza era la visualizzazione del team che possedeva entità, destinate al [supporto aggregato](https://www.martinfowler.com/bliki/DDD_Aggregate.html), sarebbe anche un'approssimazione ragionevole agli [oggetti valore](https://www.martinfowler.com/bliki/ValueObject.html). L'esperienza è stata illustrata in questo caso. Pertanto, nel EF Core 6,0 si prevede di introdurre un'esperienza migliore in base alle esigenze degli oggetti valore nella progettazione basata su dominio. Questo approccio sarà basato sui convertitori di valori anziché sulle entità di proprietà.

Questo lavoro ha inizialmente un ambito per consentire ai [convertitori di valori con mapping a più colonne](https://github.com/dotnet/efcore/issues/13947). È possibile ottenere supporto aggiuntivo in base ai commenti e suggerimenti durante la versione.

### <a name="cosmos-database-provider"></a>Provider Cosmos database

Rilevato da [problemi contrassegnati con ' area-Cosmos ' e nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)

Stato: non avviato

Dimensioni T-Shirt: grandi

Stiamo raccogliendo attivamente commenti e suggerimenti sui miglioramenti da apportare al provider Cosmos nella EF Core 6,0. Questo documento verrà aggiornato in modo più approfondito. Per il momento, assicurati di votare ( 👍 ) per le funzionalità di Cosmos che ti servono.

### <a name="expose-model-building-conventions-to-applications"></a>Esporre le convenzioni di compilazione del modello alle applicazioni

Rilevato da [#214](https://github.com/dotnet/efcore/issues/214)

Stato: non avviato

Dimensioni T-Shirt: medie

EF Core utilizza un set di convenzioni per la compilazione di un modello da tipi .NET. Queste convenzioni sono attualmente controllate dal provider di database. In EF Core 6,0, si intende consentire alle applicazioni di eseguire l'hook e modificare tali convenzioni.

### <a name="zero-bug-balance-zbb"></a>Bilanciamento del bug zero (ZBB)

Rilevato da [problemi contrassegnati con `type-bug` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)

Stato: in corso

Dimensioni T-Shirt: grandi

Si prevede di correggere tutti i bug in attesa durante l'intervallo di tempo EF Core 6,0. Alcuni aspetti da tenere presente:

- Questo vale in particolare per i problemi con etichetta [Type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).
- Vi saranno eccezioni, ad esempio quando il bug richiede una modifica di progettazione o una nuova funzionalità per correggere correttamente. Questi problemi verranno contrassegnati con l' `blocked` etichetta.
- Quando necessario, i bug si basano sul rischio, in quanto si avvicina a una versione GA/RTM.

### <a name="miscellaneous-features"></a>Funzionalità varie

Rilevato da [problemi contrassegnati con `type-enhancement` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)

Stato: in corso

Dimensioni T-Shirt: grandi

Le varie funzionalità pianificate per EF 6,0 comprendono, ma non sono limitate a:

- [Suddividere una query per le raccolte non di spostamento](https://github.com/dotnet/efcore/issues/21234)
- [Rilevare semplici tabelle di join in reverse engineering e creare relazioni molti-a-molti](https://github.com/dotnet/efcore/issues/22475)
- [Completa la ricerca full/text in SQLite e SQL Server](https://github.com/dotnet/efcore/issues/4823)
- [Indici spaziali SQL Server](https://github.com/dotnet/efcore/issues/12538)
- [Meccanismo/API per specificare una conversione predefinita per qualsiasi proprietà di un determinato tipo nel modello](https://github.com/dotnet/efcore/issues/10784)
- [Usare la nuova API per l'invio in batch da ADO.NET](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>integrazione .NET

Il team di EF Core funziona anche con diverse tecnologie correlate ma indipendenti. In particolare, si prevede di lavorare su:

### <a name="enhancements-to-systemdata"></a>Miglioramenti apportati a System. Data

Rilevato da [problemi nel repository dotnet\runtime con etichetta `area-System.Data` nell'attività cardine 6,0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)

Stato: non avviato

Dimensioni T-Shirt: grandi

Questo lavoro include:

- Implementazione della nuova [API batch](https://github.com/dotnet/runtime/issues/28633).
- Continua a collaborare con altri team .NET e la community per comprendere e sviluppare ADO.NET.
- [Standardizzare in DiagnosticSource per la traccia nei componenti System. Data. *](https://github.com/dotnet/runtime/issues/22336).

### <a name="enhancements-to-microsoftdatasqlite"></a>Miglioramenti apportati a Microsoft. Data. sqlite

Rilevato da [problemi contrassegnati con `type-enhancement` e `area-adonet-sqlite` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)

Stato: in corso

Dimensioni T-Shirt: medie

Sono previsti diversi piccoli miglioramenti per Microsoft. Data. SQLite, incluso il [pool di connessioni](https://github.com/dotnet/efcore/issues/13837) e le [istruzioni preparate](https://github.com/dotnet/efcore/issues/14044) per le prestazioni.

### <a name="nullable-reference-types"></a>Tipi riferimento nullable

Rilevato da [#14150](https://github.com/dotnet/efcore/issues/14150)

Stato: in corso

Dimensioni T-Shirt: grandi

Il codice EF Core viene annotato per usare i [tipi di riferimento Nullable](/dotnet/csharp/nullable-references).

## <a name="experiments-and-investigations"></a>Esperimenti e analisi

Il team EF sta pianificando di investire tempo durante la EF Core 6,0 di tempo di sperimentazione e analisi in due aree. Si tratta di un processo di apprendimento e, di conseguenza, non sono previsti risultati concreti per la versione 6,0.

### <a name="sqlservercore"></a>SqlServer. Core

Rilevato nel [repository Lab di dati .NET](https://github.com/dotnet/datalab/)

Stato: non avviato

Dimensioni della maglietta: in corso

[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) è un provider di database ADO.NET con funzionalità complete per SQL Server. Supporta un'ampia gamma di funzionalità di SQL Server sia in .NET Core che in .NET Framework. Tuttavia, è anche una codebase di grandi dimensioni e obsoleta con molte interazioni complesse tra i relativi comportamenti. In questo modo è difficile analizzare i potenziali guadagni che possono essere effettuati usando le funzionalità di .NET Core più recenti. Viene quindi avviato un esperimento in collaborazione con la community per determinare il potenziale potenziale per un driver di SQL Server altamente performanti per .NET.

> [!IMPORTANT]
> Gli investimenti in Microsoft. Data. SqlClient non cambiano. Continuerà a essere il metodo consigliato per connettersi a SQL Server e SQL Azure, sia con sia senza EF Core. Continuerà a supportare le nuove funzionalità di SQL Server appena introdotte.

### <a name="graphql"></a>GraphQL

Stato: non avviato

Dimensioni della maglietta: in corso

[GraphQL](https://graphql.org/) ha acquisito la trazione negli ultimi anni in un'ampia gamma di piattaforme. Si prevede di esaminare lo spazio e di trovare modi per migliorare l'esperienza con .NET. Questo comporterà la collaborazione con la community sulla comprensione e il supporto dell'ecosistema esistente. Potrebbe inoltre comportare un investimento specifico di Microsoft, sotto forma di contributi a lavoro esistente o di sviluppo di parti gratuite nello stack Microsoft.

### <a name="dataverse-formerly-common-data-services"></a>Dataverse (in precedenza Common Data Services)

Stato: non avviato

Dimensioni della maglietta: in corso

[Dataverse](/powerapps/maker/data-platform/data-platform-intro) è un archivio dati basato su colonne progettato per lo sviluppo rapido di applicazioni aziendali. Gestisce automaticamente tipi di dati complessi come oggetti binari (BLOB) e include entità e relazioni predefinite, come organizzazioni e contatti. È disponibile un SDK, ma gli sviluppatori possono trarre vantaggio dalla presenza di un provider EF Core per l'uso di query LINQ avanzate, sfruttare i vantaggi dell'unità di lavoro e disporre di un'API di accesso ai dati coerente. Il team considererà diversi modi per migliorare l'esperienza di sviluppo .NET che si connette a dataverse.

## <a name="below-the-cut-line"></a>Sotto-linea-taglia

Rilevato da [problemi con `consider-for-current-release` etichetta](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)

Si tratta di correzioni di bug e miglioramenti che **non** sono attualmente pianificati per la versione 6,0, ma verranno esaminati in base ai commenti e suggerimenti in tutta la versione insieme ai progressi apportati al lavoro precedente. Questi problemi possono essere inseriti EF Core 6,0 e diventano automaticamente candidati per la versione successiva.

Inoltre, consideriamo sempre i [problemi più votati](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) durante la pianificazione. Il taglio di uno qualsiasi di questi problemi da un rilascio è sempre penoso, ma è necessario un piano realistico per le risorse disponibili. Assicurarsi di aver votato ( 👍 ) per le funzionalità necessarie.

## <a name="suggestions"></a>Suggerimenti

I commenti e i suggerimenti dei clienti sulla pianificazione sono importanti. Il modo migliore per indicare l'importanza di un problema consiste nel votare ( 👍 ) per il problema in GitHub. Questi dati vengono quindi inseriti nel [processo di pianificazione](xref:core/what-is-new/release-planning) per la versione successiva.
