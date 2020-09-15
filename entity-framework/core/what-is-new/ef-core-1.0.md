---
title: Novità di EF Core 1.0 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 1,0
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 898f243bace7f38ffb6b216af0bd9249d73f2fe4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072460"
---
# <a name="features-included-in-ef-core-10"></a>Funzionalità incluse in EF Core 1.0

## <a name="platforms"></a>Piattaforme

### <a name="net-framework-451"></a>.NET Framework 4.5.1

Include la console, WPF, Windows Form, ASP.NET 4 e così via.

### <a name="net-standard-13"></a>.NET Standard 1.3

Include ASP.NET Core per .NET Framework e .NET Core in Windows, OSX e Linux.

## <a name="modelling"></a>Modellazione

### <a name="basic-modelling"></a>Modellazione di base

Basata sulle entità POCO con le proprietà get/set dei tipi scalari comuni (`int`, `string` e così via).

### <a name="relationships-and-navigation-properties"></a>Relazioni e proprietà di navigazione

Nel modello basato su una chiave esterna si possono specificare relazioni uno-a-molti e uno-a-zero-o-a-uno. A queste relazioni possono essere associate le proprietà di navigazione di semplici tipi di raccolta o riferimento.

### <a name="built-in-conventions"></a>Convenzioni predefinite

Compilano un modello iniziale basato sulla forma delle classi di entità.

### <a name="fluent-api"></a>API Fluent

Consente di eseguire l'override del metodo `OnModelCreating` nel contesto per ulteriori configurazioni del modello individuato dalla convenzione.

### <a name="data-annotations"></a>Annotazioni dei dati

Sono attributi che possono essere aggiunti alle classi/proprietà dell'entità e influenzeranno il modello di EF. Ad esempio, l'aggiunta di `[Required]` indicherà a EF che una proprietà è obbligatoria.

### <a name="relational-table-mapping"></a>Mapping tabella relazionale

Consente di eseguire il mapping delle entità a tabelle/colonne.

### <a name="key-value-generation"></a>Generazione di valori di chiave

Include la generazione lato client e la generazione del database.

### <a name="database-generated-values"></a>Valori generati dal database

Consente la generazione di valori dal database in caso di inserimento (valori predefiniti) o di aggiornamento (colonne calcolate).

### <a name="sequences-in-sql-server"></a>Sequenze in SQL Server

Consente la definizione di oggetti sequenza nel modello.

### <a name="unique-constraints"></a>Vincoli UNIQUE

Consente la definizione una chiave alternativa e di relazioni che specificano come destinazione tale chiave.

### <a name="indexes"></a>Indici

La definizione automatica di indici nel modello introduce gli indici nel database. Sono supportati anche gli indici univoci.

### <a name="shadow-state-properties"></a>Proprietà con stato shadow

Consente di definire nel modello proprietà che non vengono dichiarate né archiviate nella classe .NET, ma di cui è possibile tenere traccia e che possono essere aggiornate da EF Core. Questa funzionalità viene in genere usata per le proprietà di una chiave esterna quando non si vuole esporle nell'oggetto.

### <a name="table-per-hierarchy-inheritance-pattern"></a>Modello di tabella per gerarchia di ereditarietà

Consente di salvare le entità di una gerarchia di ereditarietà in una singola tabella usando una colonna discriminante per identificare il tipo di entità per un determinato record del database.

### <a name="model-validation"></a>Convalida modello

Rileva i criteri non validi nel modello e visualizza utili messaggi di errore.

## <a name="change-tracking"></a>Change tracking

### <a name="snapshot-change-tracking"></a>Rilevamento modifiche basato su snapshot

Consente di rilevare automaticamente le modifiche nelle entità confrontando lo stato corrente con una copia (snapshot) dello stato originale.

### <a name="notification-change-tracking"></a>Rilevamento modifiche con notifica

Consente alle entità di notificare all'utilità di rilevamento modifiche quando vengono modificati i valori di una proprietà.

### <a name="accessing-tracked-state"></a>Accesso allo stato registrato

Tramite `DbContext.Entry` e `DbContext.ChangeTracker`.

### <a name="attaching-detached-entitiesgraphs"></a>Collegamento di entità o grafi scollegati

La nuova API `DbContext.AttachGraph` consente di ricollegare le entità a un contesto per salvare entità nuove/modificate.

## <a name="saving-data"></a>Salvataggio dei dati

### <a name="basic-save-functionality"></a>Funzionalità di salvataggio di base

Consente di rendere persistenti nel database le modifiche apportate alle istanze delle entità.

### <a name="optimistic-concurrency"></a>Concorrenza ottimistica

Protegge dalla sovrascrittura delle modifiche apportate da un altro utente dopo che i dati sono stati recuperati dal database.

### <a name="async-savechanges"></a>SaveChanges asincrono

Consente di liberare il thread corrente per elaborare altre richieste mentre il database elabora i comandi eseguiti da `SaveChanges`.

### <a name="database-transactions"></a>Transazioni del database

Indica che `SaveChanges` è sempre atomico, ovvero che ha avuto esito completamente positivo o che non sono state apportate modifiche al database. Esistono anche API relative alle transazioni che consentono la condivisione delle transazioni tra le istanze del contesto e così via.

### <a name="relational-batching-of-statements"></a>Relazionale: invio in batch di istruzioni

Consente di ottenere migliori prestazioni inviando in batch più comandi INSERT/UPDATE/DELETE in un singolo round trip al database.

## <a name="query"></a>Query

### <a name="basic-linq-support"></a>Supporto LINQ di base

Consente di usare LINQ per recuperare i dati dal database.

### <a name="mixed-clientserver-evaluation"></a>Valutazione client/server mista

Consente alle query di contenere la logica che non può essere valutata nel database e deve quindi essere valutata dopo che i dati sono stati recuperati nella memoria.

### <a name="notracking"></a>NoTracking

Consente un'esecuzione più rapida delle query quando il contesto non deve monitorare le modifiche alle istanze delle entità (utile quando i risultati sono di sola lettura).

### <a name="eager-loading"></a>Caricamento eager

Fornisce i metodi `Include` e `ThenInclude` per identificare i dati correlati che devono anche essere recuperati durante le query.

### <a name="async-query"></a>Query asincrona

Consente di liberare il thread corrente e le risorse associate per elaborare altre richieste mentre il database elabora la query.

### <a name="raw-sql-queries"></a>Query SQL non elaborate

Questa funzionalità fornisce il metodo `DbSet.FromSql` per usare le query SQL non elaborate per recuperare i dati. Queste query possono anche essere composte per l'uso di LINQ.

## <a name="database-schema-management"></a>Gestione dello schema del database

### <a name="database-creationdeletion-apis"></a>API di creazione/eliminazione del database

Sono progettate principalmente per i test in cui si vuole creare/eliminare rapidamente il database senza usare le migrazioni.

### <a name="relational-database-migrations"></a>Migrazioni di database relazionali

Consentono l'evoluzione di uno schema del database relazionale nel corso del tempo in base alle modifiche del modello.

### <a name="reverse-engineer-from-database"></a>Reverse engineering dal database

Esegue lo scaffolding di un modello di EF basato su uno schema del database relazionale esistente.

## <a name="database-providers"></a>Provider di database

### <a name="sql-server"></a>SQL Server

Si connette a Microsoft SQL Server 2008 e versioni successive.

### <a name="sqlite"></a>SQLite

Si connette a un database SQLite 3.

### <a name="in-memory"></a>In memoria

È progettato per consentire facilmente il test senza connettersi a un vero database.

### <a name="3rd-party-providers"></a>Provider di terze parti

Sono disponibili diversi provider per gli altri motori di database. Per un elenco completo, vedere [Database Providers](xref:core/providers/index) (Provider di database).
