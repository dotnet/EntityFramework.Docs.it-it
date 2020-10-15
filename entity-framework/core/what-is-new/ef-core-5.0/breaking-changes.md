---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065641"
---
# <a name="breaking-changes-in-ef-core-50"></a>Modifiche di rilievo nella EF Core 5,0

Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.

## <a name="summary"></a>Riepilogo

| **Modifica di rilievo**                                                                                                                   | **Impatto** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Obbligatorio per la navigazione da principale a dipendente con semantica diversa](#required-dependent)                                 | Media     |
| [La definizione della query viene sostituita con metodi specifici del provider](#defining-query)                                                          | Media     |
| [Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS](#geometric-sqlite)                                                   | Basso        |
| [Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria](#cosmos-partition-key)                                                        | Basso        |
| [Cosmos: `id` proprietà rinominata in `__id`](#cosmos-id)                                                                                 | Basso        |
| [Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)                                             | Basso        |
| [Cosmos: GetPropertyName e SetPropertyName sono stati rinominati](#cosmos-metadata)                                                          | Basso        |
| [I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato](#non-added-generation) | Basso        |
| [IMigrationsModelDiffer USA ora IRelationalModel](#relational-model)                                                                 | Basso        |
| [I discriminatori sono di sola lettura](#read-only-discriminators)                                                                             | Basso        |
| [EF specifico del provider. Metodi di funzioni generate per il provider InMemory](#no-client-methods)                                              | Basso        |
| [IndexBuilder. HasName è ora obsoleto](#index-obsolete)                                                                               | Basso        |
| [Un pluarlizer è ora incluso per i modelli di impalcatura decodificati](#pluralizer)                                                 | Basso        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS

[Rilevamento del problema #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Comportamento precedente**

HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry. Tuttavia, la creazione del database ha avuto solo effetto. Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive. Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nuovo comportamento**

Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna. Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.

**Perché**

L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.

**Soluzioni di prevenzione**

Utilizzare `HasColumnType` per specificare la dimensione:

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obbligatorio per la navigazione da principale a dipendente con semantica diversa

[Rilevamento del problema #17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Comportamento precedente**

È possibile configurare solo le navigazioni all'entità come richiesto. Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.

**Nuovo comportamento**

Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.

`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**Perché**

Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).

**Soluzioni di prevenzione**

Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria

[Rilevamento del problema #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Comportamento precedente**

La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .

**Nuovo comportamento**

La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.

**Perché**

Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.

**Soluzioni di prevenzione**

Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: `id` proprietà rinominata in `__id`

[Rilevamento del problema #17751](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**Comportamento precedente**

Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .

**Nuovo comportamento**

La proprietà shadow creata per convenzione è ora denominata `__id` .

**Perché**

Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.

**Soluzioni di prevenzione**

Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri

[Rilevamento del problema #17306](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**Comportamento precedente**

Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.

**Nuovo comportamento**

Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.

**Perché**

Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.

**Soluzioni di prevenzione**

I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento. Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName e SetPropertyName sono stati rinominati

[Rilevamento del problema #17874](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**Comportamento precedente**

In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`

**Nuovo comportamento**

L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`

**Perché**

Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.

**Soluzioni di prevenzione**

Usare la nuova API.

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato

[Rilevamento del problema #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Comportamento precedente**

I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.

**Nuovo comportamento**

I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.

**Perché**

Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.

**Soluzioni di prevenzione**

Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer USA ora IRelationalModel

[Rilevamento del problema #20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**Comportamento precedente**

`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .

**Nuovo comportamento**

`IMigrationsModelDiffer` API USA ora `IRelationalModel` . Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.

**Perché**

`IRelationalModel` rappresentazione appena aggiunta dello schema del database. Il suo utilizzo per individuare le differenze è più rapido e accurato.

**Soluzioni di prevenzione**

Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>I discriminatori sono di sola lettura

[Rilevamento del problema #21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**Comportamento precedente**

È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`

**Nuovo comportamento**

Nel caso precedente verrà generata un'eccezione.

**Perché**

EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.

**Soluzioni di prevenzione**

Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>La definizione della query viene sostituita con metodi specifici del provider

[Rilevamento del problema #18903](https://github.com/dotnet/efcore/issues/18903)

**Comportamento precedente**

È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core. Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.

**Nuovo comportamento**

Le API per la definizione della query sono deprecate. Sono state introdotte nuove API specifiche del provider.

**Perché**

Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:

- Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.
- Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.

Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider In-Memory per le entità senza chiave (analogamente alle viste di database nei database relazionali). Questa definizione consente di testare facilmente l'applicazione in base al database in memoria. In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento. Quindi abbiamo deciso di semplificare il concetto. È stata creata una query di definizione basata su LINQ esclusiva per In-Memory provider e gestirli in modo diverso. Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).

**Soluzioni di prevenzione**

Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.
Per il provider di In-Memory, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>EF specifico del provider. Metodi di funzioni generate per il provider InMemory

[Rilevamento del problema #20294](https://github.com/dotnet/efcore/issues/20294)

**Comportamento precedente**

EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory. Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.

**Nuovo comportamento**

I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.

**Perché**

I metodi specifici del provider vengono mappati a una funzione di database. Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ. È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client. Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client. Poiché il provider InMemory è un *database*diverso, questi metodi non sono disponibili per questo provider. Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.

**Soluzioni di prevenzione**

Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder. HasName è ora obsoleto

[Rilevamento del problema #21089](https://github.com/dotnet/efcore/issues/21089)

**Comportamento precedente**

In precedenza era possibile definire un solo indice su un determinato set di proprietà. Il nome del database di un indice è stato configurato con IndexBuilder. HasName.

**Nuovo comportamento**

Sono ora consentiti più indici nello stesso set o proprietà. Questi indici sono ora distinti da un nome nel modello. Per convenzione, il nome del modello viene utilizzato come nome del database. Tuttavia, può anche essere configurato in modo indipendente utilizzando HasDatabaseName.

**Perché**

In futuro, vorremmo consentire l'abilitazione di indici sia ascendenti che decrescenti con regole di confronto diverse nello stesso set di proprietà. Questa modifica consente di spostarsi in un altro passaggio della direzione.

**Soluzioni di prevenzione**

Il codice precedentemente chiamato IndexBuilder. HasName deve essere aggiornato in modo da chiamare HasDatabaseName.

Se il progetto include migrazioni generate prima della versione 2.0.0 di EF Core, è possibile ignorare l'avviso in tali file ed eliminarlo aggiungendo `#pragma warning disable 612, 618` .

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Un pluarlizer è ora incluso per i modelli di impalcatura decodificati

[Rilevamento del problema #11160](https://github.com/dotnet/efcore/issues/11160)

**Comportamento precedente**

In precedenza era necessario installare un pacchetto pluralizer separato per plurali DbSet e i nomi di navigazione della raccolta e i nomi di tabella singolari quando scaffoding un DbContext e i tipi di entità reverse engineering uno schema di database.

**Nuovo comportamento**

EF Core ora include un pluralizer che usa la libreria [umanizzator](https://github.com/Humanizr/Humanizer) . Si tratta della stessa libreria utilizzata da Visual Studio per consigliare i nomi delle variabili.

**Perché**

L'uso di forme plurali di parole per le proprietà della raccolta e i moduli singolari per i tipi e le proprietà di riferimento è idiomatiche in .NET.

**Soluzioni di prevenzione**

Per disabilitare pluralizer, utilizzare l' `--no-pluralize` opzione on `dotnet ef dbcontext scaffold` o l'opzione `-NoPluralize` on `Scaffold-DbContext` .
