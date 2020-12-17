---
title: Strumenti ed estensioni - EF Core
description: Strumenti ed estensioni esterni per Entity Framework Core
author: ErikEJ
ms.date: 11/22/2020
uid: core/extensions/index
ms.openlocfilehash: 4136710567d0f6b088a0dd5ad9895d8b93313e5e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635406"
---
# <a name="ef-core-tools--extensions"></a>Strumenti ed estensioni di EF Core

Questi strumenti ed estensioni offrono funzionalità aggiuntive per Entity Framework Core 2.1 e versioni successive.

> [!IMPORTANT]  
> Le estensioni sono composte da diversi tipi di origine e non vengono mantenute nell'ambito del progetto di Entity Framework Core. Quando si prende in considerazione un'estensione di terze parti, valutarne con cura gli aspetti relativi a qualità, licenze, compatibilità, supporto e così via, per essere certi che soddisfi i propri requisiti. In particolare, un'estensione compilata per una versione precedente di EF Core potrebbe necessitare di un aggiornamento prima che funzioni con le versioni più recenti.

## <a name="tools"></a>Strumenti

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro è una soluzione per la modellazione delle entità con supporto per Entity Framework ed Entity Framework Core. Consente di definire facilmente il modello di entità e di eseguirne il mapping al database, usando l'approccio Database-First o Model-First, in modo da iniziare subito a scrivere le query. Per EF Core: 2, 3.

[Sito Web](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer è una potente finestra di progettazione di O/RM per ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access e LINQ to SQL. Supporta la progettazione visiva di modelli EF Core, usando l'approccio con precedenza del modello o precedenza del database e la generazione di codice C# o Visual Basic. Per EF Core: 2, 3, 5.

[Sito Web](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>ORM nHydrate per Entity Framework

Oggetto O/RM che crea classi estendibili e fortemente tipizzate per Entity Framework. Il codice generato è Entity Framework Core. Non c'è alcuna differenza. Non si tratta di una sostituzione per EF o un O/RM personalizzato. Si tratta di un livello di modellazione visivo che consente a un team di gestire schemi di database complessi. Funziona bene con software SCM come Git, consentendo l'accesso multiutente al modello con conflitti minimi. Il programma di installazione tiene traccia delle modifiche del modello e crea gli script di aggiornamento. Per EF Core: 3.

[Sito GitHub](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools è un'estensione di Visual Studio che espone varie attività di progettazione di EF Core in un'interfaccia utente intuitiva. Include la decompilazione di classi DbContext e classi di entità da database esistenti e [pacchetti di applicazione livello dati SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), la gestione delle migrazioni del database e le visualizzazioni del modello. Per EF Core: 3, 5.

[Wiki di GitHub](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework Visual Editor

Entity Framework editor visivo è un'estensione di Visual Studio che aggiunge una finestra di progettazione O/RM per la progettazione visiva di EF 6 e le classi EF Core. Il codice viene generato usando i modelli T4, pertanto può essere personalizzato per soddisfare qualsiasi esigenza. Supporta l'ereditarietà, le associazioni unidirezionali e bidirezionali, le enumerazioni e la possibilità di usare una codifica a colori per le classi e di aggiungere blocchi di testo, per spiegare parti potenzialmente molto complesse del progetto. Per EF Core: 2, 3, 5.

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory è un motore di scaffolding per .NET Core in grado di automatizzare la generazione di classi, entità, configurazioni di mapping e classi repository DbContext da un database SQL Server. Per EF Core: 2.

[Repository GitHub](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>Generatore Entity Framework Core di LoreSoft

Il Generatore Entity Framework Core (efg) è uno strumento della riga di comando di .NET Core che genera modelli EF Core da un database esistente con modalità simili a `dotnet ef dbcontext scaffold`, ma supporta anche la [rigenerazione](https://efg.loresoft.com/en/latest/regeneration/) del codice sicuro tramite la sostituzione dell'area o tramite l'analisi dei file di mapping. Lo strumento supporta la generazione di modelli di visualizzazione, la convalida e il codice mapper degli oggetti. Per EF Core: 2.

[Esercitazione](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentazione](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>Estensioni

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

Libreria di plug-in che consente di registrare automaticamente le modifiche ai dati eseguite da EF Core in una tabella di cronologia. Per EF Core: 2, 3.

[Repository GitHub](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

La memorizzazione nella cache di secondo livello è una cache della query. I risultati dei comandi EF verranno memorizzati nella cache, in modo che gli stessi comandi EF recupereranno i dati dalla cache anziché eseguirli di nuovo sul database. Per EF Core: 3, 5.

[Repository GitHub](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (Generator Console) è un generatore di codice semplice basato su un progetto di console, che viene eseguito in .NET Core e usa stringhe interpolate C# per la generazione di codice. Geco include un generatore di modelli inversi per EF Core con supporto di pluralizzazione, singolarizzazione e modelli modificabili. Include anche un generatore di script per dati di inizializzazione e uno strumento di pulizia database. Per EF Core: 2.

[Repository GitHub](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars

Consente la personalizzazione di classi decompilate da un database esistente usando la toolchain di Entity Framework Core con modelli Handlebars. Per EF Core: 2, 3, 5.

[Repository GitHub](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq estende i provider LINQ, ad esempio Entity Framework, e consente il riuso delle funzioni, la riscrittura di query e la creazione di query dinamiche usando predicati e selettori traducibili. Per EF Core: 2, 3, 5.

[Repository GitHub](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Plug-in per Microsoft.EntityFrameworkCore che supporta repository, modelli di unità di lavoro e più database con la transazione distribuita supportata. Per EF Core: 2, 3.

[Repository GitHub](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

Estensioni EF Core per operazioni in blocco (inserimento, aggiornamento, eliminazione). Per EF Core: 2, 3.

[Repository GitHub](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

Aggiunge la pluralizzazione in fase di progettazione. Per EF Core: 2, 3.

[Repository GitHub](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

Ripresa dell'attributo [Index] (con estensione per la compilazione del modello). Per EF Core: 2, 3, 5.

[Repository GitHub](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

Definisce un wrapper per il Provider di database in memoria EF Core. Ne rende il funzionamento più simile a quello di un provider relazionale. Per EF Core: 2.

[Repository GitHub](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

Implementazione di supporto temporale. Per EF Core: 2.

[Repository GitHub](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

Consente di eseguire facilmente query temporali nel database preferito usando metodi di estensione introdotti: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`. Per EF Core: 3.

[Repository GitHub](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Libreria di estensioni per Entity Framework Core che consente agli sviluppatori che usano SQL Server di usare facilmente le tabelle temporali. Per EF Core: 2, 3.

[Repository GitHub](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

Cache della query di secondo livello ad alte prestazioni. Per EF Core: 2.

[Repository GitHub](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

Il provider Entity Framework Core NCache è un provider di cache di secondo livello distribuito per la memorizzazione nella cache dei risultati delle query. L'architettura distribuita di NCache ne migliora la scalabilità e offre disponibilità elevata. Per EF Core 2, 3.

[Sito Web](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a>EntityFrameworkCore. triggered

Trigger per EF Core. Rispondere alle modifiche apportate in DbContext prima e dopo che ne è stato eseguito il commit nel database. I trigger sono completamente asincroni e supportano l'inserimento delle dipendenze, l'ereditarietà, la propagazione e altro ancora. Per EF Core: 3, 5.

[Repository GitHub](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a>Entity Framework Plus

Estende DbContext con funzionalità quali: filtro di inclusione, controllo, memorizzazione nella cache, query Future, eliminazione in batch, aggiornamento in batch e molte altre. Per EF Core: 2, 3, 5.

[Sito Web](https://entityframework-plus.net/)
[Repository GitHub](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework Extensions

Estende DbContext con operazioni in blocco ad alte prestazioni: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge e altre. Per EF Core: 2, 3.

[Sito Web](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

Aggiunta del supporto per la chiamata di metodi di estensione nelle espressioni lambda LINQ. Per EF Core: 3.

[Repository GitHub](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a>ELinq

Tecnologia LINQ (Language Integrated Query) per database relazionali. Consente di usare C# per scrivere query fortemente tipizzate. Per EF Core: 3.

- Supporto C# completo per la creazione di query: più istruzioni all'interno di lambda, variabili, funzioni e così via.
- Nessun gap semantico con SQL. ELinq dichiara istruzioni SQL (ad esempio `SELECT` , `FROM` , `WHERE` ) come metodi C# di prima classe, combinando sintassi familiare con IntelliSense, indipendenza dai tipi e refactoring.

Di conseguenza, SQL diventa semplicemente "un'altra" libreria di classi che espone l'API in locale, letteralmente *"SQL integrato nel linguaggio"* .

[Sito Web](https://entitylinq.com/)

### <a name="ramses"></a>Ramses

Hook del ciclo di vita (per SaveChanges). Per EF Core: 2, 3.

[Repository GitHub](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

Per tutti i nomi di tabella e colonna verrà automaticamente usata la convenzione di denominazione snake_case, tutte maiuscole o tutte minuscole. Per EF Core: 3.

[Repository GitHub](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

Aggiunge il supporto nativo a EntityFrameworkCore per SQL Server per i tipi NodaTime. Per EF Core: 3, 5.

[Repository GitHub](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Estensioni LINQ per Entity Framework Core 3.1 per supportare le query di tabelle temporali di Microsoft SQL Server. Per EF Core: 3.

[Repository GitHub](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

Aggiunge il supporto di hierarchyid al provider SQL Server EF Core. Per EF Core: 3.

[Repository GitHub](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db.EntityFrameworkCore

Convertitore alternativo di query LINQ in espressioni SQL. Per EF Core: 3, 5.

Include il supporto per le funzionalità avanzate di SQL, ad esempio espressioni di tabella comuni, copia bulk, hint di tabella, funzioni finestra, tabelle temporanee e operazioni di creazione/aggiornamento/eliminazione sul lato database.

[Repository GitHub](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

Implementazione per l'eliminazione temporanea di entità. Per EF Core: 3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a>EntityFrameworkCore.ConfigurationManager

Estende EF Core per risolvere le stringhe di connessione da App.config. Per EF Core: 3.

[Repository GitHub](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a>Mapper scollegato

Un DTO-Entity Mapper con gestione composizione/aggregazione (simile a GraphDiff). Per EF Core: 3, 5.

[NuGet](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)
