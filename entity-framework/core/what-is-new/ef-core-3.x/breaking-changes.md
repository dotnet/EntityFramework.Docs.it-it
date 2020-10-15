---
title: Modifiche di rilievo in EF Core 3. x-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: a656f3182c57689fea076ed2c7731e37fe1c4a28
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065667"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a>Modifiche di rilievo incluse in EF Core 3. x

Le modifiche alle API e al comportamento seguenti possono causare l'interruzione delle applicazioni esistenti durante l'aggiornamento a 3. x.
Le modifiche che si prevede abbiano impatto solo sui provider di database sono documentate nelle [modifiche che influiscono sul provider](xref:core/providers/provider-log).

## <a name="summary"></a>Riepilogo

| **Modifica di rilievo**                                                                                               | **Impatto** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [Le query LINQ non vengono più valutate nel client](#linq-queries-are-no-longer-evaluated-on-the-client)         | Alto       |
| [EF Core 3.0 usa come destinazione .NET Standard 2.1 invece che .NET Standard 2.0](#netstandard21) | Alto      |
| [Lo strumento da riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK](#dotnet-ef) | Alto      |
| [DetectChanges rispetta i valori di chiave generati dall'archivio](#dc) | Alto      |
| [I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati](#fromsql) | Alto      |
| [I tipi di query vengono consolidati con tipi di entità](#qt) | Alto      |
| [Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core](#no-longer) | Media      |
| [Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita](#cascade) | Media      |
| [Il caricamento eager delle entità correlate ora si verifica in una singola query](#eager-loading-single-query) | Media      |
| [Semantica più chiara per DeleteBehavior.Restrict](#deletebehavior) | Media      |
| [L'API di configurazione per le relazioni di tipo di proprietà è stata modificata](#config) | Media      |
| [Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti](#each) | Media      |
| [Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità](#notrackingresolution) | Media      |
| [Modifiche dell'API dei metadati](#metadata-api-changes) | Media      |
| [Modifiche dell'API dei metadati specifiche del provider](#provider) | Media      |
| [Il metodo UseRowNumberForPaging è stato rimosso](#urn) | Media      |
| [Non è possibile comporre il metodo dati da tabelle se usato con stored procedure](#fromsqlsproc) | Media      |
| [I metodi FromSql possono essere specificati solo in radici di query](#fromsql) | Basso      |
| [~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata](#qe) | Basso      |
| [I valori di chiave temporanei non sono più impostati nelle istanze di entità](#tkv) | Basso      |
| [Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative](#de) | Basso      |
| [Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà](#aes) | Basso      |
| [Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento](#owned-query) | Basso      |
| [Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati](#ip) | Basso      |
| [La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza](#fkp) | Basso      |
| [La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope](#dbc) | Basso      |
| [I campi sottostanti vengono usati per impostazione predefinita](#backing-fields-are-used-by-default) | Basso      |
| [Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili](#throw-if-multiple-compatible-backing-fields-are-found) | Basso      |
| [I nomi delle proprietà solo campo devono corrispondere al nome di campo](#field-only-property-names-should-match-the-field-name) | Basso      |
| [AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache](#adddbc) | Basso      |
| [AddEntityFramework * aggiunge IMemoryCache con un limite di dimensioni](#addentityframework-adds-imemorycache-with-a-size-limit) | Basso      |
| [DbContext.Entry esegue ora un DetectChanges locale](#dbe) | Basso      |
| [Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita](#string-and-byte-array-keys-are-not-client-generated-by-default) | Basso      |
| [ILoggerFactory è ora un servizio con ambito](#ilf) | Basso      |
| [I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | Basso      |
| [La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | Basso      |
| [Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa](#nbh) | Basso      |
| [Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask](#rtnt) | Basso      |
| [L'annotazione Relational:TypeMapping è ora TypeMapping](#rtt) | Basso      |
| [ToTable in un tipo derivato genera un'eccezione](#totable-on-a-derived-type-throws-an-exception) | Basso      |
| [EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite](#pragma) | Basso      |
| [Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3](#sqlite3) | Basso      |
| [I valori Guid vengono ora archiviati come TEXT in SQLite](#guid) | Basso      |
| [I valori char vengono ora archiviati come testo in SQLite](#char) | Basso      |
| [Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica](#migid) | Basso      |
| [Info/metadati dell'estensione rimossi da IDbContextOptionsExtension](#xinfo) | Basso      |
| [LogQueryPossibleExceptionWithAggregateOperator è stato rinominato](#lqpe) | Basso      |
| [Chiarimenti per l'API per i nomi di vincolo di chiave esterna](#clarify) | Basso      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici](#irdc2) | Basso      |
| [Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency](#dip) | Basso      |
| [Aggiornamento di SQLitePCL.raw alla versione 2.0.0](#SQLitePCL) | Basso      |
| [NetTopologySuite aggiornato alla versione 2.0.0](#NetTopologySuite) | Basso      |
| [Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient](#SqlClient) | Basso      |
| [Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse](#mersa) | Basso      |
| [DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello](#udf-empty-string) | Basso      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>Le query LINQ non vengono più valutate nel client

[Rilevamento del problema #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935) 
 [Vedere anche problema #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

**Comportamento precedente**

Nelle versioni precedenti alla versione 3.0, quando EF Core non era in grado di convertire un'espressione inclusa in una query in SQL o in un parametro, l'espressione veniva automaticamente valutata nel client.
Per impostazione predefinita, la valutazione client di espressioni potenzialmente dispendiose si limitava ad attivare solo un avviso.

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core consente solo la valutazione delle espressioni nella proiezione di primo livello (l'ultima chiamata `Select()` nella query) nel client.
Quando le espressioni in altre posizioni all'interno della query non possono essere convertite in SQL o in un parametro, viene generata un'eccezione.

**Perché**

La valutazione client automatica delle query consente di eseguire numerose query anche nel caso in cui parti importanti delle query non possono essere convertite.
Questo comportamento può causare un comportamento imprevisto e potenzialmente dannoso che può diventare evidente solo in produzione.
Ad esempio, una condizione in una chiamata `Where()` che non può essere convertita può causare il trasferimento di tutte le righe della tabella del server di database e l'applicazione del filtro nel client.
È probabile che questa situazione non venga rilevata se la tabella contiene solo alcune righe in fase di sviluppo, ma che abbia un grande impatto quando l'applicazione passa in produzione dove la tabella può contenere milioni di righe.
Gli avvisi di valutazione client inoltre si sono rivelati molto facili da ignorare durante lo sviluppo.

Inoltre, la valutazione client automatica può causare problemi in cui il miglioramento della conversione di query per espressioni specifiche causa modifiche impreviste che causano un'interruzione da una versione all'altra.

**Soluzioni di prevenzione**

Se una query non può essere convertita completamente, riscrivere la query in un formato che possa essere convertito o usare `AsEnumerable()`, `ToList()` o un elemento simile per riportare in modo esplicito i dati nel client dove possono essere quindi ulteriormente elaborati usando LINQ to Objects.

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a>EF Core 3.0 usa come destinazione .NET Standard 2.1 invece che .NET Standard 2.0

[Problema n. 15498](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT]
> EF Core 3,1 di nuovo la destinazione .NET Standard 2,0. Questa operazione riporta il supporto per .NET Framework.

**Comportamento precedente**

Prima della versione 3.0, EF Core usava come destinazione .NET Standard 2.0 e poteva essere eseguito in tutte le piattaforme che supportano tale standard, incluso .NET Framework.

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core usa come destinazione .NET Standard 2.1 e verrà eseguito in tutte le piattaforme che supportano questo standard. .NET Framework non è incluso.

**Perché**

Questo comportamento deriva da una decisione strategica per le tecnologie .NET, che mira a concentrare le energie su .NET Core e su altre piattaforme .NET moderne, ad esempio Xamarin.

**Soluzioni di prevenzione**

Usare EF Core 3,1.

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core

[Annunci problema n. 325](https://github.com/aspnet/Announcements/issues/325)

**Comportamento precedente**

Nelle versioni precedenti ad ASP.NET Core 3.0, quando si aggiungeva un riferimento a un pacchetto in `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, veniva inserito EF Core e alcuni dei provider di dati di EF Core come il provider di SQL Server.

**Nuovo comportamento**

A partire dalla versione 3.0, il framework condiviso di ASP.NET Core non include EF Core o provider di dati di EF Core.

**Perché**

Prima di questa modifica, per ottenere EF Core erano necessarie procedure diverse, a seconda che l'applicazione avesse o meno come destinazione ASP.NET Core e SQL Server.
Inoltre, l'aggiornamento di ASP.NET Core forzava l'aggiornamento di EF Core e del provider di SQL Server, non sempre auspicabile.

Con questa modifica, la procedura per ottenere EF Core è la stessa in tutti i provider, le implementazioni .NET supportate e i tipi di applicazioni.
Gli sviluppatori possono ora controllare anche in modo preciso quando vengono aggiornati EF Core e i provider di dati di EF Core.

**Soluzioni di prevenzione**

Per usare EF Core in un'applicazione ASP.NET Core 3.0 o in un'altra applicazione supportata, aggiungere in modo esplicito un riferimento al pacchetto al provider di database di EF Core che verrà usato dall'applicazione.

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>Lo strumento della riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK

[Problema n. 14016](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

**Comportamento precedente**

Prima della versione 3.0, lo strumento `dotnet ef` era incluso in .NET Core SDK ed era immediatamente disponibile dalla riga di comando di qualsiasi progetto senza richiedere passaggi aggiuntivi.

**Nuovo comportamento**

A partire dalla versione 3.0, .NET SDK non include lo strumento `dotnet ef` pertanto, prima di poterlo usare, è necessario installarlo in modo esplicito come strumento locale o globale.

**Perché**

Questa modifica consente di distribuire e aggiornare `dotnet ef` come uno strumento della riga di comando di .NET in NuGet, coerentemente con il fatto che anche EF Core 3.0 viene distribuito come pacchetto NuGet.

**Soluzioni di prevenzione**

Per essere in grado di gestire le migrazioni o eseguire lo scaffolding di `DbContext`, installare `dotnet-ef` come strumento globale:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

È inoltre possibile ottenerlo come strumento locale quando si ripristinano le dipendenze di un progetto che lo dichiara come dipendenza di strumenti utilizzando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati

[Problema n. 10996](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

**Comportamento precedente**

Prima di EF Core 3.0, erano disponibili overload per questi nomi di metodo per supportare l'uso con una stringa normale o una stringa che deve essere interpolata in SQL e parametri.

**Nuovo comportamento**

A partire da EF Core 3.0, usare `FromSqlRaw`, `ExecuteSqlRaw` e `ExecuteSqlRawAsync` per creare una query con parametri in cui i parametri vengono passati separatamente dalla stringa di query.
Ad esempio:

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

Usare `FromSqlInterpolated`, `ExecuteSqlInterpolated`, e `ExecuteSqlInterpolatedAsync` per creare una query con parametri in cui i parametri vengono passati come parte di una stringa di query interpolata.
Ad esempio:

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

Si noti che entrambe le query precedenti produrranno lo stesso codice SQL con parametri con gli stessi parametri SQL.

**Perché**

Con gli overload di metodi come questi, è molto facile chiamare accidentalmente il metodo con stringa non elaborata anche se l'intento era chiamare il metodo con stringa interpolata e viceversa.
Il risultato potrebbero essere query senza parametri, quando invece è prevista la parametrizzazione.

**Soluzioni di prevenzione**

Passare all'uso dei nuovi nomi di metodo.

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>Non è possibile comporre il metodo dati da tabelle se usato con stored procedure

[Rilevamento del problema #15392](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

**Comportamento precedente**

Prima di EF Core 3,0, il metodo dati da tabelle ha tentato di rilevare se il SQL passato può essere composto in base a. Ha fatto la valutazione del client quando SQL era non componibile come un stored procedure. La query seguente ha funzionato eseguendo il stored procedure sul server e FirstOrDefault sul lato client.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

**Nuovo comportamento**

A partire da EF Core 3,0, EF Core non tenterà di analizzare SQL. Quindi, se si esegue la composizione dopo FromSqlRaw/FromSqlInterpolated, EF Core comporrà il SQL causando una sottoquery. Quindi, se si usa un stored procedure con Composition, si otterrà un'eccezione per la sintassi SQL non valida.

**Perché**

EF Core 3,0 non supporta la valutazione automatica del client perché è stata soggetta a errori, come illustrato [qui](#linq-queries-are-no-longer-evaluated-on-the-client).

**Mitigazione**

Se si usa un stored procedure in FromSqlRaw/FromSqlInterpolated, è noto che non è possibile componerlo, quindi è possibile aggiungere __AsEnumerable/AsAsyncEnumerable__ subito dopo la chiamata al metodo dati da tabelle per evitare qualsiasi composizione sul lato server.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>I metodi FromSql possono essere specificati solo in radici di query

[Problema n. 15704](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

**Comportamento precedente**

Prima di EF Core 3.0, il metodo `FromSql` poteva essere specificato in un punto qualsiasi nella query.

**Nuovo comportamento**

A partire da EF Core 3.0, i nuovi metodi `FromSqlRaw` e `FromSqlInterpolated` (che sostituiscono`FromSql`) possono essere specificati solo per radici di query, ad esempio direttamente in `DbSet<>`. Qualsiasi tentativo di specificarli altrove causerà un errore di compilazione.

**Perché**

La specifica di `FromSql` in qualsiasi posizione diversa da un `DbSet` non ha alcun significato aggiuntivo oppure valore aggiunto e può causare ambiguità in determinati scenari.

**Soluzioni di prevenzione**

Le chiamate di `FromSql` devono essere spostate in modo da comparire direttamente nel `DbSet` a cui si applicano.

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità

[Problema n. 13518](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

**Comportamento precedente**

Prima di EF Core 3.0, la stessa istanza di un'entità poteva essere usata per ogni occorrenza di un entità con tipo e ID specifici. Questo comportamento corrisponde a quello delle query con rilevamento delle modifiche. Ad esempio, la query seguente:

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
restituisce la stessa istanza di `Category` per ogni `Product` associato alla categoria specificata.

**Nuovo comportamento**

A partire da EF Core 3.0, vengono create istanze di entità diverse quando un'entità con un determinato tipo e ID viene rilevata in posizioni diverse nel grafico restituito. La query precedente, ad esempio, ora restituirà una nuova istanza di `Category` per ogni `Product` anche quando due prodotti sono associati alla stessa categoria.

**Perché**

La risoluzione delle identità (ovvero il processo per determinare che un'entità ha lo stesso tipo e ID di un'entità rilevata in precedenza) aggiunge un ulteriore sovraccarico della memoria e delle prestazioni, il che va ad annullare il vantaggio derivante dall'uso delle query senza rilevamento delle modifiche. Inoltre, anche se in alcuni casi la risoluzione delle identità può essere utile, tuttavia non è necessaria se le entità devono essere serializzate e inviate a un client, come avviene comunemente con le query senza rilevamento delle modifiche.

**Soluzioni di prevenzione**

Se la risoluzione delle identità è necessaria, usare una query con rilevamento delle modifiche.

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata

[Problema n. 14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

Questa modifica è stata annullata perché la nuova configurazione in EF Core 3.0 consente all'applicazione di specificare il livello di log per qualsiasi evento. Ad esempio, per impostare la registrazione di SQL sul livello `Debug`, configurare il livello in modo esplicito in `OnConfiguring` o `AddDbContext`:
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>I valori di chiave temporanei non sono più impostati nelle istanze di entità

[Problema n. 12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 i valori temporanei venivano assegnati a tutte le proprietà di chiave per cui veniva in seguito generato un valore reale dal database.
In genere questi valori temporanei erano numeri negativi elevati.

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core archivia il valore di chiave temporaneo come parte delle informazioni di rilevamento dell'entità e non modifica la proprietà di chiave.

**Perché**

Questa modifica è stata apportata per impedire che i valori di chiave temporanei diventino erroneamente permanenti quando un'entità rilevata in precedenza da un'istanza `DbContext` viene spostata in un'altra istanza `DbContext`.

**Soluzioni di prevenzione**

Le applicazioni che assegnano valori di chiave primaria in chiavi esterne per creare associazioni tra le entità possono dipendere dal comportamento precedente se le chiavi primarie vengono generate dall'archivio e appartengono a entità con stato `Added`.
Questo può essere evitato:
* Non usando chiavi generate dall'archivio.
* Impostando le proprietà di navigazione in modo da creare relazioni anziché impostando valori di chiave esterna.
* Ottenendo i valori di chiave temporanei effettivi dalle informazioni di rilevamento dell'entità.
Ad esempio, `context.Entry(blog).Property(e => e.Id).CurrentValue` restituisce il valore temporaneo anche quando `blog.Id` non è stato impostato.

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges rispetta i valori di chiave generati dall'archivio

[Problema n. 14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 un'entità non rilevata individuata da `DetectChanges` veniva rilevata nello stato `Added` e inserita come nuova riga quando veniva eseguita una chiamata a `SaveChanges`.

**Nuovo comportamento**

A partire da EF Core 3.0, se un'entità usa valori di chiave generati e viene impostato un valore di chiave, l'entità viene rilevata nello stato `Modified`.
Ciò significa che si presuppone l'esistenza di una riga per l'entità che viene aggiornata quando viene eseguita una chiamata a `SaveChanges`.
Se il valore di chiave non viene impostato o se il tipo di entità non usa chiavi generate, la nuova entità viene rilevata come `Added` come nelle versioni precedenti.

**Perché**

Questa modifica è stata apportata per rendere più semplice e coerente l'uso di grafici di entità disconnesse con chiavi generate dall'archivio.

**Soluzioni di prevenzione**

Questa modifica può interrompere un'applicazione se un tipo di entità è configurato per l'uso di chiavi generate ma i valori di chiave sono impostati in modo esplicito per le nuove istanze.
La correzione consiste nel configurare in modo esplicito le proprietà di chiave per non usare valori generati.
Ad esempio, con l'API Fluent:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

Oppure con annotazioni dei dati:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a>Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita

[Problema n. 10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

**Comportamento precedente**

Nelle versioni precedenti alla versione 3.0, EF Core applicava azioni a catena (eliminazione delle entità dipendenti quando veniva eliminata un'entità di sicurezza obbligatoria o veniva recisa la relazione con un'entità di sicurezza obbligatoria) solo dopo la chiamata a SaveChanges.

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core applica le azioni a catena non appena viene rilevata la condizione di attivazione.
Ad esempio, la chiamata a `context.Remove()` per eliminare un'entità di sicurezza causa anche l'impostazione immediata di tutti i dipendenti obbligatori correlati rilevati su `Deleted`.

**Perché**

Questa modifica è stata apportata per migliorare l'esperienza di associazione di dati e degli scenari di controllo in cui è importante individuare le entità che verranno eliminate _prima della chiamata a _ `SaveChanges`.

**Soluzioni di prevenzione**

Il comportamento precedente può essere ripristinato tramite le impostazioni in `context.ChangeTracker`.
Ad esempio:

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>Il caricamento eager delle entità correlate ora si verifica in una singola query

[Rilevamento del problema #18022](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

**Comportamento precedente**

Prima del 3,0, il caricamento con entusiasmo degli spostamenti delle raccolte tramite `Include` gli operatori causava la generazione di più query nel database relazionale, una per ogni tipo di entità correlato.

**Nuovo comportamento**

A partire da 3,0, EF Core genera una singola query con JOIN nei database relazionali.

**Perché**

L'invio di più query per l'implementazione di una singola query LINQ ha causato numerosi problemi, incluse le prestazioni negative in quanto sono stati necessari più round trip di database e i dati coerenza problemi poiché ogni query poteva osservare uno stato diverso del database.

**Soluzioni di prevenzione**

Sebbene tecnicamente non si tratti di una modifica di rilievo, potrebbe avere un impatto significativo sulle prestazioni dell'applicazione quando una singola query contiene un numero elevato di operatori per le esplorazioni della `Include` raccolta. Per ulteriori informazioni e per riscrivere le query in modo più efficiente, [vedere il commento](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) .

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>Semantica più chiara per DeleteBehavior.Restrict

[Problema n. 12661](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

**Comportamento precedente**

Prima della versione 3.0, `DeleteBehavior.Restrict` creava chiavi esterne nel database con la semantica `Restrict`, ma modificava anche la correzione interna in modo non ovvio.

**Nuovo comportamento**

A partire dalla versione 3.0, `DeleteBehavior.Restrict` assicura che le chiavi esterne vengano create con la semantica `Restrict`, ovvero non a cascata e con generazione di un'eccezione in caso di violazione di vincolo, senza influire sulla correzione interna di Entity Framework.

**Perché**

Questa modifica è stata apportata per migliorare l'esperienza di uso di `DeleteBehavior` in modo intuitivo, senza effetti collaterali imprevisti.

**Soluzioni di prevenzione**

Il comportamento precedente può essere ripristinato tramite `DeleteBehavior.ClientNoAction`.

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a>I tipi di query vengono consolidati con tipi di entità

[Problema n. 14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 i [tipi di query](xref:core/modeling/keyless-entity-types) erano uno strumento per eseguire query su dati che non definiscono una chiave primaria in modo strutturato.
Veniva infatti usato un tipo di query per eseguire il mapping di tipi di entità senza chiavi (più probabilmente da una vista, ma anche da una tabella), mentre veniva usato un tipo di entità normale quando era disponibile una chiave (più probabilmente da una tabella, ma anche da una vista).

**Nuovo comportamento**

Un tipo di query diventa ora semplicemente un tipo di entità senza chiave primaria.
I tipi di entità senza chiave hanno la stessa funzionalità dei tipi di query nelle versioni precedenti.

**Perché**

Questa modifica è stata apportata per ridurre la confusione riguardo lo scopo dei tipi di query.
In particolare, si tratta di tipi di entità senza chiave che sono intrinsecamente di sola lettura per questo motivo ma non dovrebbero essere usati solo perché un tipo di entità deve essere di sola lettura.
Analogamente, spesso vengono mappati alle viste solo perché le viste spesso non definiscono le chiavi.

**Soluzioni di prevenzione**

Le parti dell'API seguenti sono ora obsolete:

* **`ModelBuilder.Query<>()`** -È invece necessario `ModelBuilder.Entity<>().HasNoKey()` chiamare il metodo per contrassegnare un tipo di entità senza chiavi.
Non ne viene eseguita la configurazione per convenzione per evitare una configurazione errata quando è prevista una chiave primaria che tuttavia non corrisponde alla convenzione.
* **`DbQuery<>`** - `DbSet<>` È invece consigliabile usare.
* **`DbContext.Query<>()`** - `DbContext.Set<>()` È invece consigliabile usare.
* **`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` Usare invece * *.

> [!NOTE]
> A causa di [un problema in 3. x](https://github.com/dotnet/efcore/issues/19537) durante l'esecuzione di query su entità senza chiave che dispongono di tutte le proprietà impostate su `null` un `null` verrà restituito al posto di un'entità, se il problema è applicabile anche allo scenario, aggiungere la logica per gestire `null` i risultati.

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>L'API di configurazione per le relazioni di tipo di proprietà è stata modificata

[Rilevamento del problema #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444) 
 [Rilevamento del problema #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148) 
 [Rilevamento del problema #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 la configurazione della relazione di proprietà veniva eseguita direttamente dopo la chiamata a `OwnsOne` o `OwnsMany`.

**Nuovo comportamento**

A partire da EF Core 3.0, è disponibile l'API Fluent per configurare una proprietà di navigazione per il proprietario usando `WithOwner()`.
Ad esempio:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

La configurazione correlata alla relazione tra proprietario e elemento di proprietà deve essere ora concatenata dopo `WithOwner()` in modo analogo a come vengono configurate altre relazioni.
La configurazione per il tipo di proprietà viene comunque concatenata dopo `OwnsOne()/OwnsMany()`.
Ad esempio:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");

        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

Inoltre, la chiamata a `Entity()`, `HasOne()` o `Set()` con una destinazione di tipo di proprietà genera ora un'eccezione.

**Perché**

Questa modifica è stata apportata per creare una separazione più netta tra la configurazione del tipo di proprietà e la _relazione_ con il tipo di proprietà.
Ciò consente di eliminare ambiguità e confusione su metodi come `HasForeignKey`.

**Soluzioni di prevenzione**

Modificare la configurazione delle relazioni dei tipi di proprietà per usare la superficie della nuova API come illustrato nell'esempio precedente.

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative

[Problema n. 9005](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

**Comportamento precedente**

Si consideri il modello seguente:
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o viene mappato in modo esplicito alla stessa tabella, era sempre necessaria un'istanza di `OrderDetails` per l'aggiunta di un nuovo `Order`.


**Nuovo comportamento**

A partire dalla versione 3.0, EF Core consente di aggiungere un `Order` senza un `OrderDetails` ed esegue il mapping di tutte le proprietà di `OrderDetails`, tranne che la chiave primaria, a colonne che ammettono valori Null.
In fase di query, EF Core imposta `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.

**Soluzioni di prevenzione**

Se il modello include una tabella condivisa dipendente con tutte le colonne facoltative, ma è previsto che la navigazione che punta a essa non sia `null`, l'applicazione deve essere modificata per gestire casi in cui la navigazione è `null`. Se questo non è possibile, è consigliabile aggiungere una proprietà obbligatoria al tipo di entità o assegnare un valore non `null` ad almeno una proprietà.

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà

[Problema n. 14154](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

**Comportamento precedente**

Si consideri il modello seguente:
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o è mappato in modo esplicito alla stessa tabella, il solo aggiornamento di `OrderDetails` non aggiornerà il valore `Version` nel client e l'aggiornamento successivo avrà esito negativo.


**Nuovo comportamento**

A partire dalla versione 3.0, EF Core propaga il nuovo valore `Version` a `Order` se è proprietario di `OrderDetails`. In caso contrario, viene generata un'eccezione durante la convalida del modello.

**Perché**

Questa modifica è stata apportata per evitare un valore del token di concorrenza non aggiornato quando viene aggiornata solo una delle entità mappate alla stessa tabella.

**Soluzioni di prevenzione**

Tutte le entità che condividono la tabella devono includere una proprietà mappata alla colonna del token di concorrenza. È possibile crearne una in stato shadow:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a>Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento

[Rilevamento del problema #18876](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

**Comportamento precedente**

Prima di EF Core 3,0, le entità di proprietà potevano essere sottoposte a query come qualsiasi altra navigazione.

```csharp
context.People.Select(p => p.Address);
```

**Nuovo comportamento**

A partire da 3,0, EF Core genererà un'operazione se una query di rilevamento proietta un'entità di proprietà senza proprietario.

**Perché**

Le entità di proprietà non possono essere modificate senza il proprietario, quindi nella maggior parte dei casi l'esecuzione di query in questo modo è un errore.

**Soluzioni di prevenzione**

Se l'entità di proprietà deve essere rilevata in modo da essere modificata in un secondo momento, il proprietario deve essere incluso nella query.

In caso contrario `AsNoTracking()` , aggiungere una chiamata:

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati

[Problema n. 13998](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

**Comportamento precedente**

Si consideri il modello seguente:
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

Prima di EF Core 3.0, per la proprietà `ShippingAddress` sarebbe stato eseguito il mapping a colonne separate per `BulkOrder` e `Order` per impostazione predefinita.

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core crea solo una colonna per `ShippingAddress`.

**Perché**

Il comportamento precedente non era previsto.

**Soluzioni di prevenzione**

È ancora possibile eseguire il mapping esplicito della proprietà a una colonna separata per i tipi derivati:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza

[Problema n. 13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

**Comportamento precedente**

Si consideri il modello seguente:
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
Nelle versioni precedenti a EF Core 3.0 veniva usata la proprietà `CustomerId` per la chiave esterna per convenzione.
Tuttavia, se `Order` è un tipo di proprietà, `CustomerId` sarebbe la chiave primaria e ciò non è in genere auspicabile.

**Nuovo comportamento**

A partire da 3.0, EF Core non tenta di usare le proprietà per le chiavi esterne per convenzione se hanno lo stesso nome della proprietà dell'entità di sicurezza.
Viene ancora eseguita la corrispondenza tra i criteri del nome del tipo dell'entità di sicurezza concatenato al nome della proprietà dell'entità di sicurezza e il nome di navigazione concatenato al nome della proprietà dell'entità di sicurezza.
Ad esempio:

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**Perché**

Questa modifica è stata apportata per evitare una definizione errata della proprietà di chiave primaria nel tipo di proprietà.

**Soluzioni di prevenzione**

Se la proprietà è stata progettata per essere la chiave esterna e di conseguenza parte della chiave primaria, configurarla in modo esplicito come chiave esterna.

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope

[Problema n. 14218](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

**Comportamento precedente**

Prima di EF Core 3.0, se il contesto apre la connessione all'interno di un `TransactionScope`, la connessione rimane aperta mentre è attivo il `TransactionScope` corrente.

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

**Nuovo comportamento**

A partire dalla versione 3.0, EF Core chiude la connessione non appena non viene più usata.

**Perché**

Questa modifica consente di usare più contesti nello stesso `TransactionScope`. Il nuovo comportamento corrisponde anche a EF6.

**Soluzioni di prevenzione**

Se la connessione deve rimanere aperta, la chiamata esplicita di `OpenConnection()` garantirà che EF Core non la chiuda prematuramente:

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti

[Problema n. 6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 veniva usato un unico generatore di valori condiviso per tutte le proprietà di chiavi di tipo intero in memoria.

**Nuovo comportamento**

A partire da EF Core 3.0, ogni proprietà di chiave di tipo intero riceve un generatore di valori quando viene usato il database in memoria.
Inoltre, se il database viene eliminato, la generazione di chiavi viene reimpostata per tutte le tabelle.

**Perché**

Questa modifica è stata apportata per allineare maggiormente la generazione di chiavi in memoria alla generazione di chiavi del database reale e per migliorare la possibilità di isolare i test l'uno dall'altro quando viene usato il database in memoria.

**Soluzioni di prevenzione**

Ciò può interrompere un'applicazione che si basa sull'impostazione di valori di chiave in memoria specifici.
È consigliabile non basare l'applicazione su valori di chiave specifici o eseguire l'aggiornamento per passare al nuovo comportamento.

### <a name="backing-fields-are-used-by-default"></a>I campi sottostanti vengono usati per impostazione predefinita

[Problema n. 12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

**Comportamento precedente**

Nelle versioni precedenti alla versione 3.0, anche se il campo sottostante di una proprietà era noto, per impostazione predefinita EF Core eseguiva la lettura e la scrittura del valore della proprietà usando i metodi getter e setter della proprietà.
L'eccezione era costituita dall'esecuzione di query in cui il campo sottostante, se noto, veniva impostato direttamente.

**Nuovo comportamento**

A partire da EF Core 3.0, se il campo sottostante di una proprietà è noto, la lettura e la scrittura della proprietà vengono sempre eseguite usando il campo sottostante.
Ciò potrebbe causare un'interruzione dell'applicazione se l'applicazione si basa su un comportamento aggiuntivo codificato nei metodi getter o setter.

**Perché**

Questa modifica è stata apportata per impedire a EF Core di attivare per errore la logica di business per impostazione predefinita quando si eseguono operazioni di database che interessano le entità.

**Soluzioni di prevenzione**

È possibile ripristinare il comportamento delle versioni precedenti alla versione 3.0 tramite la configurazione della modalità di accesso delle proprietà in `ModelBuilder`.
Ad esempio:

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili

[Problema n. 12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, se più campi soddisfacevano le regole di ricerca del campo sottostante di una proprietà, veniva selezionato un solo campo in base a un ordine di precedenza.
Ciò poteva causare l'uso di un campo non corretto nei casi ambigui.

**Nuovo comportamento**

A partire da EF Core 3.0, se più campi corrispondono alla stessa proprietà, viene generata un'eccezione.

**Perché**

Questa modifica è stata apportata per evitare di usare automaticamente un campo rispetto a un altro quando un solo campo può essere quello corretto.

**Soluzioni di prevenzione**

Per le proprietà con campi sottostanti ambigui, il campo da usare deve essere specificato in modo esplicito.
Ad esempio, con l'API Fluent:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>I nomi delle proprietà solo campo devono corrispondere al nome di campo

**Comportamento precedente**

Prima di EF Core 3,0, una proprietà può essere specificata da un valore stringa e, se non è stata trovata alcuna proprietà con tale nome nel tipo .NET, EF Core tenterà di associarla a un campo usando le regole di convenzione.

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

**Nuovo comportamento**

A partire da EF Core 3.0, una proprietà solo campo deve corrispondere esattamente al nome del campo.

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

**Perché**

Questa modifica è stata introdotta per evitare di usare lo stesso campo per due proprietà con nome simile. Le regole di corrispondenza per le proprietà solo campo sono state anche uniformate a quelle per le proprietà mappate a proprietà CLR.

**Soluzioni di prevenzione**

Le proprietà solo campo devono avere lo stesso nome del campo a cui vengono mappate.
In una versione futura di EF Core dopo il 3,0, si prevede di abilitare di nuovo in modo esplicito il nome di un campo diverso dal nome della proprietà (vedere il problema [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache

[Problema n. 14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

**Comportamento precedente**

Prima di EF Core 3,0, chiamando `AddDbContext` o `AddDbContextPool` sarebbero stati registrati anche i servizi di memorizzazione nella cache e di registrazione della memoria con le chiamate a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) e [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

**Nuovo comportamento**

A partire da EF Core 3.0, `AddDbContext` e `AddDbContextPool` non registreranno più questi servizi con inserimento delle dipendenze.

**Perché**

EF Core 3.0 non richiede che questi servizi siano inclusi nel contenitore di inserimento delle dipendenze dell'applicazione. Tuttavia, se `ILoggerFactory` è registrato nel contenitore di inserimento delle dipendenze dell'applicazione, verrà ancora usato da EF Core.

**Soluzioni di prevenzione**

Se l'applicazione necessita di questi servizi, registrarli in modo esplicito con il contenitore di inserimento delle dipendenze usando [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a>AddEntityFramework * aggiunge IMemoryCache con un limite di dimensioni

[Rilevamento del problema #12905](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

**Comportamento precedente**

Prima di EF Core 3,0, i metodi di chiamata `AddEntityFramework*` registravano anche i servizi di memorizzazione nella cache di memoria con di senza un limite di dimensioni.

**Nuovo comportamento**

A partire da EF Core 3,0, registrerà `AddEntityFramework*` un servizio IMemoryCache con un limite di dimensioni. Se altri servizi aggiunti successivamente dipendono da IMemoryCache, è possibile raggiungere rapidamente il limite predefinito che causa eccezioni o prestazioni ridotte.

**Perché**

L'utilizzo di IMemoryCache senza un limite può comportare l'utilizzo di memoria non controllata se è presente un bug nella logica di memorizzazione nella cache delle query o se le query vengono generate in modo dinamico. La presenza di un limite predefinito attenua un potenziale attacco DoS.

**Soluzioni di prevenzione**

Nella maggior parte dei casi, la chiamata a `AddEntityFramework*` non è necessaria se `AddDbContext` `AddDbContextPool` viene chiamato anche o. Pertanto, la migliore mitigazione consiste nel rimuovere la `AddEntityFramework*` chiamata.

Se l'applicazione necessita di questi servizi, registrare un'implementazione di IMemoryCache in modo esplicito con il contenitore DI inserimento in anticipo usando [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry esegue ora un DetectChanges locale

[Problema n. 13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 la chiamata a `DbContext.Entry` causava il rilevamento delle modifiche per tutte le entità rilevate.
Ciò garantiva l'aggiornamento dello stato esposto in `EntityEntry`.

**Nuovo comportamento**

A partire da EF Core 3.0, la chiamata a `DbContext.Entry` causa ora solo il tentativo di rilevare le modifiche nell'entità specificata e in tutte le relative entità di sicurezza rilevate.
Ciò significa che le modifiche apportate altrove potrebbero non essere state rilevate tramite la chiamata al metodo e ciò potrebbe avere implicazioni sullo stato dell'applicazione.

Si noti che se `ChangeTracker.AutoDetectChangesEnabled` è impostato su `false`, verrà disabilitato anche questo tipo di rilevamento delle modifiche locali.

Gli altri metodi che causano il rilevamento delle modifiche, ad esempio `ChangeTracker.Entries` e `SaveChanges`, causano ancora un `DetectChanges` completo di tutte le entità rilevate.

**Perché**

Questa modifica è stata apportata per migliorare le prestazioni predefinite dell'uso di `context.Entry`.

**Soluzioni di prevenzione**

Chiamare `ChangeTracker.DetectChanges()` in modo esplicito prima di chiamare `Entry` per garantire il comportamento precedente alla versione 3.0.

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita

[Problema n. 14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 le proprietà di chiave `string` e `byte[]` potevano essere usate senza impostare in modo esplicito un valore non Null.
In questi casi, il valore di chiave veniva generato nel client come GUID, serializzato in byte per `byte[]`.

**Nuovo comportamento**

A partire da EF Core 3.0 viene generata un'eccezione che indica che non è stato impostato alcun valore di chiave.

**Perché**

Questa modifica è stata apportata poiché i valori `string`/`byte[]` generati dal client non risultano in genere utili e il comportamento predefinito rendeva complesso ragionare sui valori di chiave generati in un modo comune.

**Soluzioni di prevenzione**

È possibile ripristinare il comportamento precedente alla versione 3.0 specificando in modo esplicito che le proprietà di chiave devono usare i valori generati se non viene impostato alcun altro valore non Null.
Ad esempio, con l'API Fluent:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

Oppure con annotazioni dei dati:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory è ora un servizio con ambito

[Problema n. 14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 `ILoggerFactory` veniva registrato come servizio singleton.

**Nuovo comportamento**

A partire da EF Core 3.0, `ILoggerFactory` viene registrato come servizio con ambito.

**Perché**

Questa modifica è stata apportata per consentire l'associazione di un logger a un'istanza `DbContext` che abilita altre funzionalità e rimuove alcuni casi di comportamento anomalo, ad esempio un'esplosione dei provider di servizi interni.

**Soluzioni di prevenzione**

Questa modifica non dovrebbe influire sul codice dell'applicazione a meno che non vengano registrati e usati servizi personalizzati nel provider di servizi interno di EF Core.
Questo non è un comportamento comune.
In questi casi la maggior parte delle operazioni continuano a essere eseguite correttamente, ma qualsiasi servizio singleton dipendente da `ILoggerFactory` dovrà essere modificato per ottenere `ILoggerFactory` in modo diverso.

Se si verificano situazioni simili, inviare una segnalazione nello [strumento di gestione dei problemi in GitHub relativo a EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) per comunicare in che modo viene usato `ILoggerFactory` per consentirci di comprendere meglio come evitare ulteriori interruzioni in futuro.

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente

[Problema n. 12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, quando `DbContext` veniva eliminato non esisteva alcun metodo per scoprire se una determinata proprietà di navigazione in un'entità ottenuta da un contesto specifico veniva caricata completamente o meno.
I proxy presupponevano invece che venisse caricata una navigazione di riferimento se era presente un valore non Null e che venisse caricata una navigazione di raccolta se era presente un valore.
In questi casi il tentativo di eseguire un caricamento lazy non avrebbe avuto alcun esito.

**Nuovo comportamento**

A partire da Entity Framework Core 3.0, i proxy tengono traccia del caricamento o mancato caricamento di una proprietà di navigazione.
Ciò significa che il tentativo di accedere a una proprietà di navigazione caricata dopo l'eliminazione del contesto non avrà mai alcun esito, anche quando la navigazione caricata è vuota o ha valore Null.
Al contrario, il tentativo di accedere a una proprietà di navigazione non caricata genera un'eccezione se il contesto viene eliminato anche se la proprietà di navigazione è una raccolta non vuota.
Se si verifica questa situazione significa che il codice dell'applicazione sta tentando di usare il caricamento lazy in un momento non valido e l'applicazione deve essere modificata in modo da non eseguire questa operazione.

**Perché**

Questa modifica è stata apportata per rendere coerente e corretto il comportamento durante un tentativo di caricamento lazy in un'istanza `DbContext` eliminata.

**Soluzioni di prevenzione**

Aggiornare il codice dell'applicazione per fare in modo che non venga tentato il caricamento lazy con un contesto eliminato oppure specificare una configurazione in modo che non venga eseguita alcuna operazione come descritto nel messaggio di eccezione.

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita

[Problema n. 10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0 veniva registrato un avviso per le applicazioni che creavano un numero eccessivo di provider di servizi interni.

**Nuovo comportamento**

A partire da EF Core 3.0, l'avviso viene considerato un errore e viene generata un'eccezione.

**Perché**

Questa modifica è stata apportata per gestire meglio il codice dell'applicazione tramite un'esposizione più esplicita di questa situazione di errore.

**Soluzioni di prevenzione**

L'azione più appropriata quando si verifica questo errore consiste nell'individuare la causa radice e nell'interrompere la creazione di numerosi provider di servizi interni.
È possibile tuttavia convertire nuovamente l'errore in avviso o ignorarlo tramite una configurazione in `DbContextOptionsBuilder`.
Ad esempio:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa

[Problema n. 9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

**Comportamento precedente**

Prima di EF Core 3.0, il codice che chiama `HasOne` o `HasMany` con una singola stringa era interpretato in modo poco chiaro.
Ad esempio:
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

Apparentemente, il codice mette in relazione `Samurai` con un altro tipo di entità tramite la proprietà di navigazione `Entrance`, che può essere privata.

In realtà, il codice tenta di creare una relazione con un tipo di entità denominato `Entrance` senza proprietà di navigazione.

**Nuovo comportamento**

A partire da EF Core 3.0, il codice sopra riportato ora esegue quello che avrebbe dovuto fare in precedenza.

**Perché**

Il comportamento precedente era molto poco chiaro, soprattutto durante la lettura del codice di configurazione e la ricerca di errori.

**Soluzioni di prevenzione**

Questa modifica causerà problemi solo nelle applicazioni che configurano relazioni in modo esplicito usando stringhe per i nomi dei tipi e senza specificare in modo esplicito la proprietà di navigazione.
Non è uno scenario comune.
Il comportamento precedente può essere ottenuto passando esplicitamente `null` per il nome della proprietà di navigazione.
Ad esempio:

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask

[Problema n. 15184](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

**Comportamento precedente**

I metodi asincroni seguenti in precedenza restituivano il tipo `Task<T>`:

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (e classi derivate)

**Nuovo comportamento**

I metodi indicati in precedenza ora restituiscono il tipo `ValueTask<T>` sullo stesso `T` come in precedenza.

**Perché**

Questa modifica riduce il numero delle allocazioni di heap sostenute quando si richiamano questi metodi, con un miglioramento generale delle prestazioni.

**Soluzioni di prevenzione**

Le applicazioni semplicemente in attesa delle API precedenti devono solo essere ricompilate e non sono richieste modifiche del codice sorgente.
Per scenari di utilizzo più complessi (ad esempio, il passaggio del tipo `Task` restituito a `Task.WhenAny()`) è richiesto in genere che il tipo `ValueTask<T>` restituito venga convertito in `Task<T>` chiamando `AsTask()` su di esso.
Si noti che in questo modo si annulla la riduzione delle allocazioni consentita da questa modifica.

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>L'annotazione Relational:TypeMapping è ora TypeMapping

[Problema n. 9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

**Comportamento precedente**

Il nome di annotazione delle annotazioni di mapping del tipo era "Relational:TypeMapping".

**Nuovo comportamento**

Il nome di annotazione delle annotazioni di mapping del tipo è ora "TypeMapping".

**Perché**

Il mapping dei tipi non viene più usato solo per i provider di database relazionali.

**Soluzioni di prevenzione**

Ciò causa un'interruzione solo nelle applicazioni che accedono al mapping dei tipi direttamente come annotazione. Questa situazione non è comune.
L'azione più appropriata per risolvere il problema consiste nell'usare la superficie dell'API per accedere al mapping dei tipi anziché l'annotazione.

### <a name="totable-on-a-derived-type-throws-an-exception"></a>ToTable in un tipo derivato genera un'eccezione

[Problema n. 11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, la chiamata a `ToTable()` in un tipo derivato veniva ignorata poiché soltanto la strategia di mapping dell'ereditarietà era una tabella per gerarchia dove la chiamata non era valida.

**Nuovo comportamento**

A partire da EF Core 3.0 e in preparazione all'aggiunta del supporto per la tabella per tipo e per TPC in una versione successiva, la chiamata a `ToTable()` in un tipo derivato genera un'eccezione per evitare una modifica del mapping imprevista in futuro.

**Perché**

Attualmente il mapping di un tipo derivato in una tabella diversa non è un'operazione valida.
Questa modifica consente di evitare interruzioni future quando l'operazione diventerà un'operazione valida.

**Soluzioni di prevenzione**

Rimuovere qualsiasi tentativo di mapping di tipi derivati in altre tabelle.

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex sostituito con HasIndex

[Problema n. 12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offriva un metodo per configurare le colonne usate con `INCLUDE`.

**Nuovo comportamento**

A partire da EF Core 3.0, l'uso di `Include` in un indice è supportato a livello relazionale.
Usare `HasIndex().ForSqlServerInclude()`.

**Perché**

Questa modifica è stata apportata per consolidare l'API per gli indici con `Include` in un'unica posizione per tutti i provider di database.

**Soluzioni di prevenzione**

Usare la nuova API, come illustrato in precedenza.

### <a name="metadata-api-changes"></a>Modifiche dell'API dei metadati

[Problema n. 214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**Nuovo comportamento**

Le proprietà seguenti sono state convertite in metodi di estensione:

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

**Perché**

Questa modifica semplifica l'implementazione delle interfacce menzionate in precedenza.

**Soluzioni di prevenzione**

Usare i nuovi metodi di estensione.

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>Modifiche dell'API dei metadati specifiche del provider

[Problema n. 214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**Nuovo comportamento**

I metodi di estensione specifici del provider verranno resi flat:

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

**Perché**

Questa modifica semplifica l'implementazione dei metodi di estensione menzionati in precedenza.

**Soluzioni di prevenzione**

Usare i nuovi metodi di estensione.

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite

[Problema n. 12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, EF Core inviava `PRAGMA foreign_keys = 1` quando veniva aperta una connessione a SQLite.

**Nuovo comportamento**

A partire da EF Core 3.0, EF Core non invia più `PRAGMA foreign_keys = 1` quando viene aperta una connessione a SQLite.

**Perché**

Questa modifica è stata apportata poiché EF Core usa `SQLitePCLRaw.bundle_e_sqlite3` per impostazione predefinita. Ciò significa che l'imposizione della chiave esterna è abilitata per impostazione predefinita e non deve essere abilitata in modo esplicito ogni volta che viene aperta una connessione.

**Soluzioni di prevenzione**

Le chiavi esterne sono abilitate per impostazione predefinita in SQLitePCLRaw.bundle_e_sqlite3, usato per impostazione predefinita per EF Core.
Per gli altri casi, è possibile abilitare le chiavi esterne specificando `Foreign Keys=True` nella stringa di connessione.

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3

**Comportamento precedente**

Nelle versioni precedenti a EF Core 3.0, EF Core usava `SQLitePCLRaw.bundle_green`.

**Nuovo comportamento**

A partire da EF Core 3.0, EF Core usa `SQLitePCLRaw.bundle_e_sqlite3`.

**Perché**

Questa modifica è stata apportata per rendere coerente la versione di SQLite usata in iOS con le altre piattaforme.

**Soluzioni di prevenzione**

Per usare la versione di SQLite nativa in iOS, configurare `Microsoft.Data.Sqlite` per l'uso di un'aggregazione `SQLitePCLRaw` diversa.

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>I valori Guid vengono ora archiviati come TEXT in SQLite

[Problema n. 15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

**Comportamento precedente**

I valori Guid in precedenza venivano archiviati come valori BLOB in SQLite.

**Nuovo comportamento**

I valori Guid vengono ora archiviati come TEXT.

**Perché**

Il formato binario dei valori Guid non è standardizzato. L'archiviazione dei valori come TEXT rende il database più compatibile con altre tecnologie.

**Soluzioni di prevenzione**

È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.

```sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite rimane in grado di leggere i valori Guid sia da colonne BLOB che TEXT. Tuttavia, poiché è stato modificato il formato predefinito per i parametri e le costanti, probabilmente sarà necessario intervenire per la maggior parte degli scenari che coinvolgono valori Guid.

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>I valori char vengono ora archiviati come testo in SQLite

[Problema n. 15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

**Comportamento precedente**

I valori char in precedenza venivano archiviati come valori interi in SQLite. Un valore char di *A* veniva ad esempio archiviato come valore intero 65.

**Nuovo comportamento**

I valori char vengono ora archiviati come testo.

**Perché**

L'archiviazione dei valori come testo è un'operazione più naturale e rende il database più compatibile con altre tecnologie.

**Soluzioni di prevenzione**

È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite rimane comunque in grado di leggere i valori di caratteri presenti sia nelle colonne di valori interi sia in quelle di testo, quindi alcuni scenari potrebbero non richiedere alcuna azione.

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica

[Problema n. 12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

**Comportamento precedente**

Gli ID di migrazione venivano inavvertitamente generati usando il calendario delle impostazioni cultura correnti.

**Nuovo comportamento**

Gli ID di migrazione ora vengono sempre generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica (calendario gregoriano).

**Perché**

L'ordine delle migrazioni è importante quando si esegue l'aggiornamento del database o si risolvono i conflitti di unione. L'uso del calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica evita i problemi che possono verificarsi quando i membri del team hanno calendari di sistema diversi.

**Soluzioni di prevenzione**

Questa modifica interessa gli utenti che usano un calendario non gregoriano in cui l'anno ha un'estensione superiore al calendario gregoriano (come il calendario buddista tailandese). Gli ID di migrazione esistenti dovranno essere aggiornati in modo che le nuove migrazioni vengano collocate dopo le migrazioni esistenti.

L'ID di migrazione è disponibile nell'attributo di migrazione presente nei file di progettazione delle migrazioni.

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

È necessario aggiornare anche la tabella della cronologia delle migrazioni.

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>Il metodo UseRowNumberForPaging è stato rimosso

[Problema n. 16400](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

**Comportamento precedente**

Prima di EF Core 3.0 si poteva usare `UseRowNumberForPaging` per generare codice SQL per la suddivisione in pagine compatibile con SQL Server 2008.

**Nuovo comportamento**

A partire da EF Core 3.0, EF genererà solo codice SQL per la suddivisione in pagine compatibile solo con versioni successive di SQL Server.

**Perché**

Questa modifica è stata apportata perché [SQL Server 2008 non è più un prodotto supportato](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) e l'aggiornamento di questa funzionalità per interagire con le modifiche apportate per le query in EF Core 3.0 è un lavoro significativo.

**Soluzioni di prevenzione**

È consigliabile eseguire l'aggiornamento a una versione più recente di SQL Server o usare un livello di compatibilità superiore, in modo che il codice SQL generato sia supportato. Detto questo, se non è possibile procedere in questo modo, [aggiungere un commento per il problema](https://github.com/aspnet/EntityFrameworkCore/issues/16400) con indicazioni dettagliate. Microsoft potrebbe rivedere questa decisione in base ai commenti e suggerimenti.

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>Info/metadati dell'estensione rimossi da IDbContextOptionsExtension

[Problema n. 16119](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

**Comportamento precedente**

`IDbContextOptionsExtension` conteneva metodi per fornire i metadati relativi all'estensione.

**Nuovo comportamento**

Questi metodi sono stati spostati in una nuova classe di base astratta `DbContextOptionsExtensionInfo`, restituita da una nuova proprietà `IDbContextOptionsExtension.Info`.

**Perché**

Nelle versioni dalla 2.0 alla 3.0 è stato necessario aggiungere o modificare questi metodi più volte.
Suddividendoli in una nuova classe di base astratta sarà più facile apportare questo tipo di modifiche senza compromettere il funzionamento delle estensioni esistenti.

**Soluzioni di prevenzione**

Aggiornare le estensioni per seguire il nuovo modello.
Sono disponibili esempi nelle numerose implementazioni di `IDbContextOptionsExtension` per diversi tipi di estensioni nel codice sorgente di EF Core.

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator è stato rinominato

[Problema n. 10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

**Modifica**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` è stato rinominato in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

**Perché**

Allineamento del nome di questo evento di avviso con tutti gli altri eventi di avviso.

**Soluzioni di prevenzione**

Usare il nuovo nome. (Si noti che il numero di ID evento non è stato modificato.)

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>Chiarimenti per l'API per i nomi di vincolo di chiave esterna

[Problema n. 10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

**Comportamento precedente**

Prima di EF Core 3.0, si faceva riferimento ai nomi di vincolo di chiave esterna semplicemente con "Name". Ad esempio:

```csharp
var constraintName = myForeignKey.Name;
```

**Nuovo comportamento**

A partire da EF Core 3.0, si fa ora riferimento ai nomi di vincolo di chiave esterna con "ConstraintName". Ad esempio:

```csharp
var constraintName = myForeignKey.ConstraintName;
```

**Perché**

Questa modifica introduce coerenza per la denominazione in quest'area e chiarisce anche che si tratta del nome del vincolo di chiave esterna e non del nome della colonna o della proprietà per cui è definita la chiave esterna.

**Soluzioni di prevenzione**

Usare il nuovo nome.

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici

[Problema n. 15997](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

**Comportamento precedente**

Prima di EF Core 3.0 questi metodi erano protetti.

**Nuovo comportamento**

A partire da EF Core 3.0 questi metodi sono pubblici.

**Perché**

Questi metodi vengono usati da EF per determinare se un database viene creato, ma vuoto. Ciò risulta utile all'esterno di EF quando occorre determinare se applicare o meno le migrazioni.

**Soluzioni di prevenzione**

Modificare l'accessibilità di eventuali override.

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency

[Problema n. 11506](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

**Comportamento precedente**

Prima di EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un pacchetto NuGet normale ed era possibile fare riferimento al relativo assembly dai progetti dipendenti.

**Nuovo comportamento**

A partire da EF Core 3.0 è un pacchetto DevelopmentDependency. Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che non è più possibile, per impostazione predefinita, fare riferimento al relativo assembly.

**Perché**

Questo pacchetto è destinato solo all'uso in fase di progettazione. Le applicazioni distribuite non devono farvi riferimento. Questa raccomandazione è rafforzata dall'impostazione del pacchetto come DevelopmentDependency.

**Soluzioni di prevenzione**

Se è necessario fare riferimento a questo pacchetto per eseguire l'override del comportamento della fase di progettazione di EF Core, è possibile aggiornare i metadati dell'elemento PackageReference nel progetto.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

In presenza di riferimenti transitivi al pacchetto tramite Microsoft.EntityFrameworkCore.Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto per modificare i relativi metadati. Un riferimento esplicito di questo tipo deve essere aggiunto a qualsiasi progetto in cui sono necessari i tipi del pacchetto.

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>Aggiornamento di SQLitePCL.raw alla versione 2.0.0

[Problema n. 14824](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

**Comportamento precedente**

Microsoft.EntityFrameworkCore.Sqlite dipendeva in precedenza dalla versione 1.1.12 di SQLitePCL.raw.

**Nuovo comportamento**

Il pacchetto è stato aggiornato in base alla versione 2.0.0.

**Perché**

La versione 2.0.0 di SQLitePCL.raw è destinata a .NET Standard 2.0. Era in precedenza destinata a .NET Standard 1.1 e ciò richiedeva un notevole impegno di chiusura di pacchetti transitivi per il funzionamento.

**Soluzioni di prevenzione**

SQLitePCL.raw versione 2.0.0 include alcune modifiche che causano un'interruzione. Per informazioni dettagliate, vedere le [Note sulla versione](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite aggiornato alla versione 2.0.0

[Problema n. 14825](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

**Comportamento precedente**

I pacchetti spaziali dipendevano in precedenza dalla versione 1.15.1 di NetTopologySuite.

**Nuovo comportamento**

Il pacchetto è stato aggiornato in modo da dipendere dalla versione 2.0.0.

**Perché**

La versione 2.0.0 di NetTopologySuite risolve vari problemi di usabilità riscontrati dagli utenti di EF Core.

**Soluzioni di prevenzione**

NetTopologySuite versione 2.0.0 include alcune modifiche che causano un'interruzione. Per informazioni dettagliate, vedere le [Note sulla versione](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient

[Rilevamento del problema #15636](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

**Comportamento precedente**

Microsoft. EntityFrameworkCore. SqlServer in precedenza dipende da System. Data. SqlClient.

**Nuovo comportamento**

Il pacchetto è stato aggiornato in base a Microsoft. Data. SqlClient.

**Perché**

Microsoft. Data. SqlClient è il driver di accesso ai dati principale per SQL Server in futuro e System. Data. SqlClient non è più l'obiettivo dello sviluppo.
Alcune funzionalità importanti, ad esempio Always Encrypted, sono disponibili solo in Microsoft. Data. SqlClient.

**Soluzioni di prevenzione**

Se il codice prende una dipendenza diretta da System. Data. SqlClient, è necessario modificarlo in modo che faccia riferimento a Microsoft. Data. SqlClient. Poiché i due pacchetti gestiscono un livello molto elevato di compatibilità API, questo dovrebbe essere solo una semplice modifica del pacchetto e dello spazio dei nomi.

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse

[Problema n. 13573](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

**Comportamento precedente**

Un tipo di entità con più proprietà di navigazione unidirezionale che fanno riferimento a se stesse e più chiavi esterne corrispondenti è stato erroneamente configurato come relazione singola. Ad esempio:

```csharp
public class User
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

**Nuovo comportamento**

Questo scenario viene ora rilevato nella compilazione del modello e viene generata un'eccezione indicante che il modello è ambiguo.

**Perché**

Il modello risultante era ambiguo e sarà probabilmente errato in questo caso.

**Soluzioni di prevenzione**

Usare la configurazione completa della relazione. Ad esempio:

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();

 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello

[Rilevamento del problema #12757](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

**Comportamento precedente**

Un DbFunction configurato con schema come stringa vuota è stato trattato come funzione predefinita senza uno schema. Il codice seguente, ad esempio, eseguirà il mapping della `DatePart` funzione CLR alla `DATEPART` funzione predefinita in SqlServer.

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

**Nuovo comportamento**

Tutti i mapping di DbFunction sono considerati mappati alle funzioni definite dall'utente. Pertanto, il valore stringa vuoto inserisce la funzione all'interno dello schema predefinito per il modello. Che può corrispondere allo schema configurato in modo esplicito tramite l'API Fluent `modelBuilder.HasDefaultSchema()` o `dbo` in caso contrario.

**Perché**

Lo schema precedentemente vuoto era un modo per trattare la funzione è incorporata, ma tale logica è applicabile solo per SqlServer, in cui le funzioni predefinite non appartengono ad alcuno schema.

**Soluzioni di prevenzione**

Configurare manualmente la conversione di DbFunction per eseguirne il mapping a una funzione predefinita.

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
