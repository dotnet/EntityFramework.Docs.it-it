---
title: Versioni precedenti di Entity Framework-EF6
description: Versioni precedenti di Entity Framework
author: divega
ms.date: 09/12/2019
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: 97ad8015f5302adbe98a63132f1b34009aa68468
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073643"
---
# <a name="past-releases-of-entity-framework"></a>Versioni precedenti di Entity Framework

La prima versione di Entity Framework è stata rilasciata in 2008, come parte di .NET Framework 3,5 SP1 e Visual Studio 2008 SP1.

A partire dalla versione 4.1 di EF è stato fornito come [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/) , attualmente uno dei pacchetti più diffusi in NuGet.org.

Tra le versioni 4,1 e 5,0, il pacchetto NuGet EntityFramework ha esteso le librerie EF fornite come parte del .NET Framework.

A partire dalla versione 6, EF è diventato un progetto open source ed è stato spostato completamente fuori banda dal .NET Framework.
Ciò significa che quando si aggiunge il pacchetto NuGet EntityFramework versione 6 a un'applicazione, si riceve una copia completa della libreria EF che non dipende dai bit EF forniti come parte del .NET Framework.
Questo ha consentito di accelerare in modo accelerato il ritmo di sviluppo e distribuzione delle nuove funzionalità.

Nel giugno 2016 abbiamo rilasciato EF Core 1,0. EF Core si basa su una nuova codebase ed è progettata come versione più leggera ed estendibile di EF.
Attualmente EF Core è l'obiettivo principale dello sviluppo del team di Entity Framework Microsoft.
Ciò significa che non sono previste nuove funzionalità principali per EF6. Tuttavia, EF6 viene comunque gestito come progetto open source e come prodotto Microsoft supportato.

Di seguito è riportato l'elenco delle versioni precedenti, in ordine cronologico inverso, con informazioni sulle nuove funzionalità introdotte in ogni versione.

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Aggiornamento di Entity Framework Tools in Visual Studio 2017 15.7
Nel mese di maggio 2018 è stata rilasciata una versione aggiornata di EF Tools come parte di Visual Studio 2017 15.7.
Include miglioramenti per alcune aree problematiche comuni:

- Correzioni di vari bug di accessibilità dell'interfaccia utente
- Soluzione per la regressione delle prestazioni di SQL Server durante la generazione di modelli da database esistenti [n. 4](https://github.com/aspnet/entityframework6/issues/4)
- Supporto dell'aggiornamento dei modelli per modelli di dimensioni superiori in SQL Server [n. 185](https://github.com/aspnet/EntityFramework6/issues/185)

Un altro miglioramento in questa nuova versione di EF Tools è l'installazione del runtime di EF 6.2 durante la creazione di un modello in un nuovo progetto. Con le versioni precedenti di Visual Studio è possibile usare il runtime di EF 6.2 (o altre versioni precedenti di EF) installando la versione corrispondente del pacchetto NuGet.

## <a name="ef-620"></a>EF 6.2.0
Il runtime di EF 6.2 è stato rilasciato in NuGet nel mese di ottobre 2017.
Grazie principalmente al lavoro svolto dalla community di contributori open source, EF 6.2 include numerose [correzioni di bug](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug) e [miglioramenti del prodotto](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20).

Di seguito viene riportato un breve elenco delle modifiche più importanti che interessano il runtime di EF 6.2:

- Riduzione del tempo di avvio grazie al caricamento di modelli Code First finiti da una cache persistente [n. 275](https://github.com/aspnet/EntityFramework6/issues/275)
- API Fluent per definire gli indici [n. 274](https://github.com/aspnet/EntityFramework6/issues/274)
- DbFunctions.Like() per abilitare la scrittura di query LINQ che vengono convertite in LIKE in SQL [n. 241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe ora supporta l'opzione -script [n. 240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 consente ora di usare i valori chiave generati da una sequenza in SQL Server [n. 165](https://github.com/aspnet/EntityFramework6/issues/165)
- Aggiornamento dell'elenco di errori temporanei per la strategia di esecuzione SQL Azure [n. 83](https://github.com/aspnet/EntityFramework6/issues/83)
- Bug: i nuovi tentativi di query o comandi SQL restituiscono l'errore "SqlParameter già contenuto in un altro elemento SqlParameterCollection" [n. 81](https://github.com/aspnet/EntityFramework6/issues/81)
- Bug: frequente timeout della valutazione di DbQuery.ToString() nel debugger [n. 73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF 6.1.3
Il runtime di EF 6.1.3 è stato rilasciato a NuGet nell'ottobre del 2015.
Questa versione contiene solo correzioni a errori e regressioni ad alta priorità segnalati nella versione 6.1.2.
Le correzioni includono:

- Query: regressione in EF 6.1.2: OUTER APPLY introdotto e query più complesse per le relazioni 1:1 e la clausola "Let"
- Problema TPT con la proprietà della classe base nascosta nella classe ereditata
- DbMigration. SQL ha esito negativo quando la parola ' Go ' è contenuta nel testo
- Crea il flag di compatibilità per UnionAll trae e il supporto bidimensionale Intersect
- La query con più inclusioni non funziona in 6.1.2 (funzionante in 6.1.1)
- "Si è verificata un'eccezione nella sintassi SQL" dopo l'aggiornamento da EF 6.1.1 a 6.1.2

## <a name="ef-612"></a>EF 6.1.2
Il runtime di EF 6.1.2 è stato rilasciato a NuGet nel dicembre 2014.
Questa versione riguarda per lo più le correzioni di bug. Sono state inoltre accettate alcune importanti modifiche da parte dei membri della community:
- **I parametri della cache di query possono essere configurati dal file app/web.configuration**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- I **Metodi sqlfile e sqlresource in DbMigration** consentono di eseguire uno script SQL archiviato come file o risorsa incorporata.

## <a name="ef-611"></a>EF 6.1.1
Il runtime di EF 6.1.1 è stato rilasciato a NuGet nel giugno 2014.
Questa versione contiene correzioni per i problemi riscontrati da un certo numero di persone. Tra gli altri:
- Finestra di progettazione: errore durante l'apertura di EF5 edmx con precisione decimale in EF6 designer
- La logica di rilevamento dell'istanza predefinita per il database locale non funziona con SQL Server 2014

## <a name="ef-610"></a>EF 6.1.0
Il runtime di EF 6.1.0 è stato rilasciato a NuGet nel marzo 2014.
Questo aggiornamento secondario include un numero significativo di nuove funzionalità:

- Il **consolidamento degli strumenti** offre un modo coerente per creare un nuovo modello EF. Questa funzionalità [estende la procedura guidata ADO.NET Entity Data Model per supportare la creazione di modelli Code First](xref:ef6/modeling/code-first/workflows/existing-database), incluso Reverse Engineering da un database esistente. Queste funzionalità in precedenza erano disponibili in qualità beta in EF Power Tools.
- La **[gestione degli errori di commit delle transazioni](xref:ef6/fundamentals/connection-resiliency/commit-failures)** fornisce CommitFailureHandler che consente di utilizzare la nuova funzionalità introdotta per intercettare le operazioni di transazione. Il CommitFailureHandler consente il ripristino automatico dagli errori di connessione durante il commit di una transazione.
- **[IndexAttribute](xref:ef6/modeling/code-first/data-annotations)** consente di specificare gli indici inserendo un `[Index]` attributo in una proprietà (o proprietà) nel modello di Code First. Code First creerà quindi un indice corrispondente nel database.
- **L'API di mapping pubblico** fornisce l'accesso alle informazioni EF sul modo in cui le proprietà e i tipi vengono mappati alle colonne e alle tabelle nel database. Nelle versioni precedenti questa API era interna.
- **[La possibilità di configurare gli intercettori tramite il file app/Web.config](xref:ef6/fundamentals/configuring/config-file)** consente di aggiungere gli intercettori senza ricompilare l'applicazione.
- **System. Data. Entity. Infrastructure. Interceptor. DatabaseLogger**è un nuovo intercettore che semplifica la registrazione di tutte le operazioni di database in un file. In combinazione con la funzionalità precedente, in questo modo è possibile [attivare facilmente la registrazione delle operazioni di database per un'applicazione distribuita](xref:ef6/fundamentals/configuring/config-file), senza dover ricompilare.
- Il **rilevamento delle modifiche del modello delle migrazioni** è stato migliorato in modo che le migrazioni con impalcature siano più accurate. sono state inoltre migliorate le prestazioni del processo di rilevamento delle modifiche.
- **Miglioramenti delle prestazioni** , tra cui operazioni di database ridotte durante l'inizializzazione, ottimizzazioni per il confronto di uguaglianza di valori null nelle query LINQ, generazione di visualizzazioni più veloce (creazione di modelli) in più scenari e materializzazione più efficiente delle entità registrate con più associazioni.

## <a name="ef-602"></a>EF 6.0.2
Il runtime di EF 6.0.2 è stato rilasciato a NuGet nel dicembre 2013.
Questa versione della patch è limitata alla risoluzione dei problemi introdotti nella versione EF6 (regressione in prestazioni/comportamento rispetto a EF5).

## <a name="ef-601"></a>EF 6.0.1
EF 6.0.1 Runtime è stato rilasciato a NuGet nell'ottobre del 2013 contemporaneamente a EF 6.0.0, perché quest'ultimo era incorporato in una versione di Visual Studio che era stata bloccata alcuni mesi prima.
Questa versione della patch è limitata alla risoluzione dei problemi introdotti nella versione EF6 (regressione in prestazioni/comportamento rispetto a EF5).
Le modifiche più rilevanti erano la risoluzione di alcuni problemi di prestazioni durante il riscaldamento per i modelli EF.
Questo aspetto era importante in quanto le prestazioni di riscaldamento erano un'area di interesse in EF6 e questi problemi negavano alcuni dei miglioramenti apportati alle prestazioni in EF6.

## <a name="ef-60"></a>EF 6,0
Il runtime di EF 6.0.0 è stato rilasciato a NuGet nell'ottobre del 2013.
Questa è la prima versione in cui è incluso un runtime EF completo nel [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/) che non dipende dai bit EF che fanno parte del .NET Framework.
Lo stato di trasferimento delle parti rimanenti del runtime al pacchetto NuGet richiedeva una serie di modifiche di rilievo per il codice esistente.
Per ulteriori informazioni sui passaggi manuali necessari per eseguire l'aggiornamento, vedere la sezione relativa all' [aggiornamento a Entity Framework 6](xref:ef6/what-is-new/upgrading-to-ef6) .

Questa versione include numerose nuove funzionalità.
Le funzionalità seguenti funzionano per i modelli creati con Code First o la finestra di progettazione EF:

- La **[query asincrona e il salvataggio](xref:ef6/fundamentals/async)** aggiungono il supporto per i modelli asincroni basati su attività introdotti in .NET 4,5.
- La **[resilienza delle connessioni](xref:ef6/fundamentals/connection-resiliency/retry-logic)** consente il ripristino automatico da errori di connessione temporanei.
- La **[configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based)** offre la possibilità di eseguire la configurazione, che in genere è stata eseguita in un file di configurazione, nel codice.
- La **[risoluzione delle dipendenze](xref:ef6/fundamentals/configuring/dependency-resolution)** introduce il supporto per il modello di localizzatore del servizio ed è stato eseguito il factoring di alcune funzionalità che possono essere sostituite con implementazioni personalizzate.
- La **[registrazione di intercettazione/SQL](xref:ef6/fundamentals/logging-and-interception)** fornisce blocchi predefiniti di basso livello per l'intercettazione di operazioni EF con una semplice registrazione SQL compilata in primo piano.
- I **miglioramenti della testabilità** semplificano la creazione di duplicati di test per DbContext e DbSet quando si [Usa un Framework fittizio](xref:ef6/fundamentals/testing/mocking) o si [scrivono doppi test personalizzati](xref:ef6/fundamentals/testing/writing-test-doubles).
- **[È ora possibile creare DbContext con un DbConnection già aperto](xref:ef6/fundamentals/connection-management)** che consente scenari in cui è utile se la connessione potrebbe essere aperta durante la creazione del contesto, ad esempio la condivisione di una connessione tra i componenti in cui non è possibile garantire lo stato della connessione.
- Il **[supporto delle transazioni migliorato](xref:ef6/saving/transactions)** fornisce il supporto per una transazione esterna al Framework, oltre a metodi migliorati per la creazione di una transazione all'interno del Framework.
- **Enumerazioni, prestazioni spaziali e migliori in .net 4,0** : spostando i componenti di base che si trovavano nel .NET Framework nel pacchetto di Entity Framework, ora è possibile offrire supporto enum, tipi di dati spaziali e miglioramenti delle prestazioni di EF5 in .NET 4,0.
- **Miglioramento delle prestazioni di Enumerable. Contains nelle query LINQ**.
- **Miglioramento del tempo di riscaldamento (generazione delle visualizzazioni)**, soprattutto per i modelli di grandi dimensioni.
- **Pluralità &amp; modulare Servizio di singolarità**.
- Sono ora supportate le **implementazioni personalizzate di Equals o GetHashCode** sulle classi di entità.
- **DbSet. AddRange/RemoveRange** fornisce un modo ottimizzato per aggiungere o rimuovere più entità da un set.
- **DbChangeTracker. HasChanges** fornisce un modo semplice ed efficiente per verificare se sono presenti modifiche in sospeso da salvare nel database.
- **SqlCeFunctions** fornisce un oggetto SQL Compact equivalente a SqlFunctions.

Le funzionalità seguenti si applicano solo a Code First:

- Le **[convenzioni Code First personalizzate](xref:ef6/modeling/code-first/conventions/custom)** consentono di scrivere convenzioni personalizzate per evitare la configurazione ripetitiva. Viene fornita un'API semplice per le convenzioni leggere, oltre ad alcuni blocchi predefiniti più complessi che consentono di creare convenzioni più complesse.
- È ora supportata la **[Code First il mapping alle stored procedure INSERT/UPDATE/DELETE](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)** .
- Gli **[script di migrazione di idempotente](xref:ef6/modeling/code-first/migrations/index)** consentono di generare uno script SQL in grado di aggiornare un database con qualsiasi versione fino alla versione più recente.
- La **[tabella di cronologia delle migrazioni configurabili](xref:ef6/modeling/code-first/migrations/history-customization)** consente di personalizzare la definizione della tabella di cronologia delle migrazioni. Questa operazione è particolarmente utile per i provider di database che richiedono i tipi di dati appropriati e così via. per il corretto funzionamento della tabella di cronologia delle migrazioni.
- Con **più contesti per database** viene rimossa la limitazione precedente di un modello di Code First per ogni database quando si utilizzano le migrazioni o quando Code First crea automaticamente il database.
- **[DbModelBuilder. HasDefaultSchema](xref:ef6/modeling/code-first/fluent/types-and-properties)** è una nuova API Code First che consente di configurare lo schema del database predefinito per un modello di Code First in un'unica posizione. In precedenza, lo schema predefinito Code First è stato hardcoded in &quot; dbo &quot; e l'unico modo per configurare lo schema a cui apparteneva una tabella era tramite l'API ToTable.
- **DbModelBuilder.Configurations. Il metodo AddFromAssembly** consente di aggiungere facilmente tutte le classi di configurazione definite in un assembly quando si utilizzano le classi di configurazione con l'API Code First Fluent.
- **[Le operazioni di migrazione personalizzate](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** consentono di aggiungere altre operazioni da usare nelle migrazioni basate sul codice.
- Il **livello di isolamento delle transazioni predefinito viene modificato in READ_COMMITTED_SNAPSHOT** per i database creati con Code First, consentendo una maggiore scalabilità e un minor numero di deadlock.
- I **tipi di entità e complessi possono ora essere classi nestedinside**.

## <a name="ef-50"></a>EF 5,0
Il runtime di EF 5.0.0 è stato rilasciato a NuGet nel 2012 agosto.
Questa versione introduce alcune nuove funzionalità, tra cui il supporto enum, le funzioni con valori di tabella, i tipi di dati spaziali e diversi miglioramenti delle prestazioni.

Il Entity Framework Designer in Visual Studio 2012 introduce anche il supporto per più diagrammi per modello, la colorazione delle forme nell'area di progettazione e l'importazione batch di stored procedure.

Di seguito è riportato un elenco di contenuto che viene raccolto in modo specifico per la versione EF 5:

-   [Post di rilascio di EF 5](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   Nuove funzionalità di EF5
    -   [Supporto enum in Code First](xref:ef6/modeling/code-first/data-types/enums)
    -   [Supporto enum in EF designer](xref:ef6/modeling/designer/data-types/enums)
    -   [Tipi di dati spaziali in Code First](xref:ef6/modeling/code-first/data-types/spatial)
    -   [Tipi di dati spaziali in EF designer](xref:ef6/modeling/designer/data-types/spatial)
    -   [Supporto del provider per i tipi spaziali](xref:ef6/fundamentals/providers/spatial-support)
    -   [Funzioni con valori di tabella](xref:ef6/modeling/designer/advanced/tvfs)
    -   [Più diagrammi per modello](xref:ef6/modeling/designer/multiple-diagrams)
-   Impostazione del modello
    -   [Creazione di un modello](xref:ef6/modeling/index)
    -   [Connessioni e modelli](xref:ef6/fundamentals/configuring/connection-strings)
    -   [Considerazioni sulle prestazioni](xref:ef6/fundamentals/performance/perf-whitepaper)
    -   [Utilizzo di Microsoft SQL Azure](xref:ef6/fundamentals/connection-resiliency/retry-logic)
    -   [Impostazioni del file di configurazione](xref:ef6/fundamentals/configuring/config-file)
    -   [Glossario](xref:ef6/resources/glossary)
    -   Code First
        -   [Code First a un nuovo database (procedura dettagliata e video)](xref:ef6/modeling/code-first/workflows/new-database)
        -   [Code First a un database esistente (procedura dettagliata e video)](xref:ef6/modeling/code-first/workflows/existing-database)
        -   [Convenzioni](xref:ef6/modeling/code-first/conventions/built-in)
        -   [Annotazioni dei dati](xref:ef6/modeling/code-first/data-annotations)
        -   [API Fluent-configurazione/mapping delle proprietà & tipi](xref:ef6/modeling/code-first/fluent/types-and-properties)
        -   [API Fluent-configurazione delle relazioni](xref:ef6/modeling/code-first/fluent/relationships)
        -   [API Fluent con VB.NET](xref:ef6/modeling/code-first/fluent/vb)
        -   [Migrazioni Code First](xref:ef6/modeling/code-first/migrations/index)
        -   [Migrazioni Code First automatico](xref:ef6/modeling/code-first/migrations/automatic)
        -   [Migrate.exe](xref:ef6/modeling/code-first/migrations/migrate-exe)
        -   [Definizione di DbSet](xref:ef6/modeling/code-first/dbsets)
    -   EF Designer
        -   [Model First (procedura dettagliata e video)](xref:ef6/modeling/designer/workflows/model-first)
        -   [Database First (procedura dettagliata e video)](xref:ef6/modeling/designer/workflows/database-first)
        -   [Tipi complessi](xref:ef6/modeling/designer/data-types/complex-types)
        -   [Associazioni/relazioni](xref:ef6/modeling/designer/relationships)
        -   [Modello di ereditarietà TPT](xref:ef6/modeling/designer/inheritance/tpt)
        -   [Modello di ereditarietà TPH](xref:ef6/modeling/designer/inheritance/tph)
        -   [Eseguire query con stored procedure](xref:ef6/modeling/designer/stored-procedures/query)
        -   [Stored procedure con più set di risultati](xref:ef6/modeling/designer/advanced/multiple-result-sets)
        -   [Insert, Update & Delete con stored procedure](xref:ef6/modeling/designer/stored-procedures/cud)
        -   [Eseguire il mapping di un'entità a più tabelle (suddivisione di entità)](xref:ef6/modeling/designer/entity-splitting)
        -   [Eseguire il mapping di più entità a una tabella (suddivisione di tabelle)](xref:ef6/modeling/designer/table-splitting)
        -   [Definizione di query](xref:ef6/modeling/designer/advanced/defining-query)
        -   [Modelli di generazione del codice](xref:ef6/modeling/designer/codegen/index)
        -   [Ripristino di ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext)
-   Uso del modello
    -   [Utilizzo di DbContext](xref:ef6/fundamentals/working-with-dbcontext)
    -   [Esecuzione di query/ricerca di entità](xref:ef6/querying/index)
    -   [Operazioni con le relazioni](xref:ef6/fundamentals/relationships)
    -   [Caricamento di entità correlate](xref:ef6/querying/related-data)
    -   [Utilizzo di dati locali](xref:ef6/querying/local-data)
    -   [Applicazioni a più livelli](xref:ef6/fundamentals/disconnected-entities/index)
    -   [Query SQL non elaborate](xref:ef6/querying/raw-sql)
    -   [Modelli di concorrenza ottimistica](xref:ef6/saving/concurrency)
    -   [Uso dei proxy](xref:ef6/fundamentals/proxies)
    -   [Rilevamento automatico delle modifiche](xref:ef6/saving/change-tracking/auto-detect-changes)
    -   [Query senza rilevamento](xref:ef6/querying/no-tracking)
    -   [Metodo Load](xref:ef6/querying/load-method)
    -   [Aggiungi/Connetti e Stati di entità](xref:ef6/saving/change-tracking/entity-state)
    -   [Utilizzo dei valori delle proprietà](xref:ef6/saving/change-tracking/property-values)
    -   [Data Binding con WPF (Windows Presentation Foundation)](xref:ef6/fundamentals/databinding/wpf)
    -   [Data Binding con WinForms (Windows Forms)](xref:ef6/fundamentals/databinding/winforms)

## <a name="ef-431"></a>EF 4.3.1
Il runtime di EF 4.3.1 è stato rilasciato a NuGet nel febbraio 2012 poco dopo EF 4.3.0.
Questa versione della patch include alcune correzioni di bug per la versione EF 4,3 e introduce un migliore supporto del database locale per i clienti che usano EF 4,3 con Visual Studio 2012.

Di seguito è riportato un elenco di contenuti riuniti in modo specifico per la versione di EF 4.3.1. la maggior parte dei contenuti disponibili per EF 4,1 si applica ancora anche a EF 4,3:

-   [Post di Blog di EF 4.3.1 versione](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4,3
Il runtime di EF 4.3.0 è stato rilasciato a NuGet nel febbraio del 2012.
Questa versione include la nuova funzionalità Migrazioni Code First che consente di modificare in modo incrementale un database creato da Code First Man mano che il modello di Code First evolve.

Di seguito è riportato un elenco di contenuti che sono stati riuniti in modo specifico per la versione EF 4,3, la maggior parte dei contenuti disponibili per EF 4,1 è ancora applicabile a EF 4,3:
-   [Post di rilascio di EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [Procedura dettagliata sulle migrazioni basate su codice EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [Procedura dettagliata per le migrazioni automatiche EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4,2
Il runtime di EF 4.2.0 è stato rilasciato a NuGet nel novembre 2011.
Questa versione include correzioni di bug per la versione 4.1.1 di EF.
Poiché in questa versione sono state incluse solo correzioni di bug, potrebbe essere stata rilasciata la patch di EF 4.1.2, ma si è scelto di passare a 4,2 per poter uscire dalla data in base ai numeri di versione patch usati nelle versioni 4.1. x e adottare lo standard di [controllo delle versioni semantico](https://semver.org) per il controllo delle versioni semantico.

Di seguito è riportato un elenco di contenuti riuniti in modo specifico per la versione EF 4,2, il contenuto fornito per EF 4,1 si applica ancora anche a EF 4,2:

-   [Post di rilascio di EF 4,2](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Procedura dettagliata Code First](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [Procedura dettagliata Database First & di modelli](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
Il runtime di EF 4.1.10715 è stato rilasciato a NuGet nel luglio 2011.
Oltre alle correzioni di bug, questa versione di patch introduce alcuni componenti per semplificare l'utilizzo di un modello di Code First per gli strumenti in fase di progettazione.
Questi componenti vengono usati da Migrazioni Code First (incluso in EF 4,3) e da EF Power Tools.

Si noterà che il numero di versione strano 4.1.10715 del pacchetto.
Abbiamo usato le versioni di patch basate su data prima di decidere di adottare il [controllo delle versioni semantico](https://semver.org).
Si pensi a questa versione come EF 4,1 patch 1 (o EF 4.1.1).

Di seguito è riportato un elenco di contenuto che viene raccolto per la versione 4.1.1:

-   [Post di rilascio di EF 4.1.1](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4,1
Il runtime di EF 4.1.10331 è stato il primo a essere pubblicato in NuGet, ad aprile 2011.
Questa versione include l'API DbContext semplificata e il flusso di lavoro Code First.

Si noterà il numero di versione strano, 4.1.10331, che dovrebbe essere effettivamente 4,1. È inoltre disponibile una versione di 4.1.10311 che dovrebbe essere 4.1.0-RC ("RC" sta per "release candidate").
Abbiamo usato le versioni di patch basate su data prima di decidere di adottare il [controllo delle versioni semantico](https://semver.org).

Di seguito è riportato un elenco di contenuto che viene raccolto per la versione 4,1. Gran parte di essa si applica ancora alle versioni successive di Entity Framework:

-   [Post di rilascio di EF 4,1](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Procedura dettagliata Code First](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [Procedura dettagliata Database First & di modelli](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure federazioni e il Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4,0
Questa versione è inclusa in .NET Framework 4 e Visual Studio 2010, ad aprile 2010.
Le nuove funzionalità importanti di questa versione includono il supporto per POCO, il mapping di chiavi esterne, il caricamento lazy, i miglioramenti della testabilità, la generazione di codice personalizzabile e il flusso di lavoro Model First.

Sebbene fosse la seconda versione di Entity Framework, era denominata EF 4 per allinearsi alla versione .NET Framework fornita con.
Al termine di questa versione, abbiamo iniziato a rendere Entity Framework disponibili in NuGet e aver adottato il controllo delle versioni semantico, perché non era più legata alla versione .NET Framework.

Si noti che alcune versioni successive di .NET Framework sono state fornite con aggiornamenti significativi per i bit EF inclusi.
Infatti, molte delle nuove funzionalità di EF 5,0 sono state implementate come miglioramenti a questi bit.
Tuttavia, per razionalizzare il controllo delle versioni per Entity Framework, continuiamo a fare riferimento ai bit EF che fanno parte del .NET Framework come il runtime EF 4,0, mentre tutte le versioni più recenti sono costituite dal [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).

## <a name="ef-35"></a>EF 3,5
La versione iniziale di Entity Framework è stata inclusa in .NET 3,5 Service Pack 1 e Visual Studio 2008 SP1, rilasciata nell'agosto di 2008.
Questa versione ha fornito il supporto di base di O/RM usando il flusso di lavoro Database First.
