---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e52b77e8c19ab3005aee50e1cf4e170d3ee1f502
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023445"
---
# <a name="breaking-changes-in-ef-core-50"></a>Modifiche di rilievo nella EF Core 5,0

Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.

## <a name="summary"></a>Riepilogo

| **Modifica di rilievo**                                                                                                                   | **Impatto** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [EF Core 5,0 non supporta .NET Framework](#netstandard21)                                                                         | Medio     |
| [IProperty. getColumnName () è obsoleto](#getcolumnname-obsolete)                                                                  | Medio     |
| [La precisione e la scala sono necessarie per i numeri decimali](#decimals)                                                                            | Medio     |
| [Obbligatorio per la navigazione da principale a dipendente con semantica diversa](#required-dependent)                                 | Medio     |
| [La definizione della query viene sostituita con metodi specifici del provider](#defining-query)                                                          | Medio     |
| [Le esplorazioni di riferimento non null non vengono sovrascritte dalle query](#nonnullreferences)                                                   | Medio     |
| [Toview () viene considerato in modo diverso dalle migrazioni](#toview)                                                                              | Medio     |
| [ToTable (null) contrassegna il tipo di entità come non mappato a una tabella](#totable)                                                              | Medio     |
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
| [Un pluralizer è ora incluso per i modelli di impalcatura decodificati](#pluralizer)                                                 | Basso        |
| [INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip](#inavigationbase)                                     | Basso        |
| [Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate](#collection-distinct-groupby) | Basso        |
| [L'uso di una raccolta di tipi queryable nella proiezione non è supportato](#queryable-projection)                                          | Basso        |

## <a name="medium-impact-changes"></a>Modifiche a media Impact

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a>EF Core 5,0 non supporta .NET Framework

[Problema n. 15498](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a>Comportamento precedente

EF Core 3,1 destinazioni .NET Standard 2,0, supportato da .NET Framework.

#### <a name="new-behavior"></a>Nuovo comportamento

EF Core 5,0 destinazioni .NET Standard 2,1, che non è supportato da .NET Framework. Ciò significa EF Core 5,0 non può essere utilizzato con .NET Framework applicazioni.

#### <a name="why"></a>Perché

Questa operazione fa parte dello spostamento più ampio tra i team .NET finalizzati all'unificazione di un singolo Framework di destinazione .NET. Per ulteriori informazioni, vedere [il futuro del .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

#### <a name="mitigations"></a>Soluzioni di prevenzione

.NET Framework applicazioni possono continuare a usare EF Core 3,1, che è una [versione di supporto a lungo termine (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). In alternativa, è possibile aggiornare le applicazioni per l'uso di .NET Core 3,1 o .NET 5, che supportano entrambe .NET Standard 2,1.

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a>IProperty. getColumnName () è obsoleto

[Rilevamento del problema #2266](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a>Comportamento precedente

`GetColumnName()` Restituisce il nome della colonna a cui è stato eseguito il mapping di una proprietà.

#### <a name="new-behavior"></a>Nuovo comportamento

`GetColumnName()` restituisce comunque il nome di una colonna a cui è stato eseguito il mapping di una proprietà, ma questo comportamento è ambiguo perché EF Core 5 supporta TPT e il mapping simultaneo a una vista o a una funzione in cui questi mapping potrebbero utilizzare nomi di colonna diversi per la stessa proprietà.

#### <a name="why"></a>Perché

Questo metodo è stato contrassegnato come obsoleto per guidare gli utenti a un sovraccarico più accurato <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .

#### <a name="mitigations"></a>Soluzioni di prevenzione

Usare il codice seguente per ottenere il nome della colonna per una tabella specifica:

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a>La precisione e la scala sono necessarie per i numeri decimali

[Rilevamento del problema #19293](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a>Comportamento precedente

EF Core in genere non ha impostato la precisione e la scala sugli <xref:Microsoft.Data.SqlClient.SqlParameter> oggetti. Ciò significa che la precisione e la scalabilità completa sono state inviate a SQL Server, a quel punto SQL Server arrotondate in base alla precisione e alla scala della colonna del database.

#### <a name="new-behavior"></a>Nuovo comportamento

EF Core ora imposta la precisione e la scalabilità sui parametri usando i valori configurati per le proprietà nel modello di EF Core. Ciò significa che l'arrotondamento si verifica ora in SqlClient. In modo consequenziale, se la precisione e la scala configurate non corrispondono alla precisione e alla scala del database, l'arrotondamento visualizzato potrebbe cambiare.

#### <a name="why"></a>Perché

Le nuove funzionalità di SQL Server, tra cui Always Encrypted, richiedono che i facet di parametro siano specificati in modo completo. Inoltre, SqlClient ha apportato una modifica a Round anziché troncare i valori decimali, in modo che corrisponda al comportamento del SQL Server. In questo modo è possibile EF Core impostare questi facet senza modificare il comportamento per i numeri decimali configurati correttamente.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Eseguire il mapping delle proprietà Decimal usando un nome di tipo che include precisione e scala. Ad esempio:

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

Oppure usare `HasPrecision` nelle API per la creazione di modelli. Ad esempio:

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obbligatorio per la navigazione da principale a dipendente con semantica diversa

[Rilevamento del problema #17286](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>Comportamento precedente

È possibile configurare solo le navigazioni all'entità come richiesto. Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.

#### <a name="new-behavior"></a>Nuovo comportamento

Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.

`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>Perché

Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).

#### <a name="mitigations"></a>Soluzioni di prevenzione

Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>La definizione della query viene sostituita con metodi specifici del provider

[Rilevamento del problema #18903](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>Comportamento precedente

È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core. Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.

#### <a name="new-behavior"></a>Nuovo comportamento

Le API per la definizione della query sono deprecate. Sono state introdotte nuove API specifiche del provider.

#### <a name="why"></a>Perché

Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:

- Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.
- Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.

Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider In-Memory per le entità senza chiave (analogamente alle viste di database nei database relazionali). Questa definizione consente di testare facilmente l'applicazione in base al database in memoria. In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento. Quindi abbiamo deciso di semplificare il concetto. È stata creata una query di definizione basata su LINQ esclusiva per In-Memory provider e gestirli in modo diverso. Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).

#### <a name="mitigations"></a>Soluzioni di prevenzione

Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.
Per il provider di In-Memory, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>Le esplorazioni di riferimento non null non vengono sovrascritte dalle query

[Rilevamento del problema #2693](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>Comportamento precedente

In EF Core 3,1, le esplorazioni dei riferimenti inizializzate in modo eager con valori non null verrebbero talvolta sovrascritte dalle istanze di entità dal database, indipendentemente dal fatto che i valori delle chiavi corrispondano o meno. Tuttavia, in altri casi, EF Core 3,1 esegue l'operazione opposta e lascia il valore non null esistente.

#### <a name="new-behavior"></a>Nuovo comportamento

A partire da EF Core 5,0, le navigazioni di riferimento non null non vengono mai sovrascritte dalle istanze restituite da una query.

Si noti che l'inizializzazione eager di una _raccolta_ di spostamento in una raccolta vuota è ancora supportata.

#### <a name="why"></a>Perché

L'inizializzazione di una proprietà di navigazione di riferimento su un'istanza di entità "vuota" restituisce uno stato ambiguo. Ad esempio:

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

In genere, una query per i Blog e gli autori creerà prima di tutto le `Blog` istanze e quindi imposterà le `Author` istanze appropriate in base ai dati restituiti dal database. Tuttavia, in questo caso ogni `Blog.Author` proprietà è già inizializzata su un oggetto vuoto `Author` . Ad eccezione di EF Core non è possibile verificare che l'istanza sia "vuota". Quindi, sovrascrivendo questa istanza potrebbe essere possibile eliminare un valore valido in modo invisibile all'utente `Author` . Pertanto, EF Core 5,0 ora non sovrascrive in modo coerente una navigazione già inizializzata.

Questo nuovo comportamento è anche allineato con il comportamento di EF6 nella maggior parte dei casi, sebbene durante l'analisi sono stati rilevati anche alcuni casi di incoerenza in EF6.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Se viene rilevata questa interruzione, la correzione consiste nell'arrestare l'inizializzazione delle proprietà di navigazione dei riferimenti.

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a>Toview () viene considerato in modo diverso dalle migrazioni

[Rilevamento del problema #2725](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a>Comportamento precedente

La chiamata a `ToView(string)` ha fatto in modo che le migrazioni ignorino il tipo di entità oltre a eseguire il mapping a una vista.

#### <a name="new-behavior"></a>Nuovo comportamento

Ora `ToView(string)` contrassegna il tipo di entità come non mappato a una tabella, oltre a eseguirne il mapping a una vista. Ciò comporta la prima migrazione dopo l'aggiornamento a EF Core 5 per tentare di eliminare la tabella predefinita per questo tipo di entità perché non viene più ignorata.

#### <a name="why"></a>Perché

EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, pertanto `ToView` non è più un indicatore valido che deve essere ignorato dalle migrazioni.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Usare il codice seguente per contrassegnare la tabella mappata come esclusa dalle migrazioni:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a>ToTable (null) contrassegna il tipo di entità come non mappato a una tabella

[Rilevamento del problema #21172](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a>Comportamento precedente

`ToTable(null)` Reimposta il nome della tabella sul valore predefinito.

#### <a name="new-behavior"></a>Nuovo comportamento

`ToTable(null)` ora contrassegna il tipo di entità come non mappato ad alcuna tabella.

#### <a name="why"></a>Perché

EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, quindi `ToTable(null)` viene usato per indicare che non è mappato ad alcuna tabella.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Usare il codice seguente per reimpostare il nome della tabella sul valore predefinito se non è mappato a una vista o a un DbFunction:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a>Modifiche a basso effetto

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS

[Rilevamento del problema #14257](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>Comportamento precedente

HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry. Tuttavia, la creazione del database ha avuto solo effetto. Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive. Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/dotnet/efcore/issues/14257)).

#### <a name="new-behavior"></a>Nuovo comportamento

Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna. Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.

#### <a name="why"></a>Perché

L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.

#### <a name="mitigations"></a>Soluzioni di prevenzione

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

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria

[Rilevamento del problema #15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Comportamento precedente

La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .

#### <a name="new-behavior"></a>Nuovo comportamento

La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.

#### <a name="why"></a>Perché

Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: `id` proprietà rinominata in `__id`

[Rilevamento del problema #17751](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>Comportamento precedente

Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .

#### <a name="new-behavior"></a>Nuovo comportamento

La proprietà shadow creata per convenzione è ora denominata `__id` .

#### <a name="why"></a>Perché

Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri

[Rilevamento del problema #17306](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>Comportamento precedente

Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.

#### <a name="new-behavior"></a>Nuovo comportamento

Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.

#### <a name="why"></a>Perché

Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.

#### <a name="mitigations"></a>Soluzioni di prevenzione

I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento. Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/dotnet/efcore/issues/17306)

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName e SetPropertyName sono stati rinominati

[Rilevamento del problema #17874](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>Comportamento precedente

In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`

#### <a name="new-behavior"></a>Nuovo comportamento

L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`

#### <a name="why"></a>Perché

Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Usare la nuova API.

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato

[Rilevamento del problema #15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Comportamento precedente

I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.

#### <a name="new-behavior"></a>Nuovo comportamento

I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.

#### <a name="why"></a>Perché

Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer USA ora IRelationalModel

[Rilevamento del problema #20305](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>Comportamento precedente

`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .

#### <a name="new-behavior"></a>Nuovo comportamento

`IMigrationsModelDiffer` API USA ora `IRelationalModel` . Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.

#### <a name="why"></a>Perché

`IRelationalModel` rappresentazione appena aggiunta dello schema del database. Il suo utilizzo per individuare le differenze è più rapido e accurato.

#### <a name="mitigations"></a>Soluzioni di prevenzione

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

[Rilevamento del problema #21154](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>Comportamento precedente

È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`

#### <a name="new-behavior"></a>Nuovo comportamento

Nel caso precedente verrà generata un'eccezione.

#### <a name="why"></a>Perché

EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>EF specifico del provider. Metodi di funzioni generate per il provider InMemory

[Rilevamento del problema #20294](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>Comportamento precedente

EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory. Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.

#### <a name="new-behavior"></a>Nuovo comportamento

I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.

#### <a name="why"></a>Perché

I metodi specifici del provider vengono mappati a una funzione di database. Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ. È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client. Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client. Poiché il provider InMemory è un *database* diverso, questi metodi non sono disponibili per questo provider. Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder. HasName è ora obsoleto

[Rilevamento del problema #21089](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>Comportamento precedente

In precedenza era possibile definire un solo indice su un determinato set di proprietà. Il nome del database di un indice è stato configurato con IndexBuilder. HasName.

#### <a name="new-behavior"></a>Nuovo comportamento

Sono ora consentiti più indici nello stesso set o proprietà. Questi indici sono ora distinti da un nome nel modello. Per convenzione, il nome del modello viene utilizzato come nome del database. Tuttavia, può anche essere configurato in modo indipendente utilizzando HasDatabaseName.

#### <a name="why"></a>Perché

In futuro, vorremmo consentire l'abilitazione di indici sia ascendenti che decrescenti con regole di confronto diverse nello stesso set di proprietà. Questa modifica consente di spostarsi in un altro passaggio della direzione.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Il codice precedentemente chiamato IndexBuilder. HasName deve essere aggiornato in modo da chiamare HasDatabaseName.

Se il progetto include migrazioni generate prima della versione 2.0.0 di EF Core, è possibile ignorare l'avviso in tali file ed eliminarlo aggiungendo `#pragma warning disable 612, 618` .

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Un pluralizer è ora incluso per i modelli di impalcatura decodificati

[Rilevamento del problema #11160](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>Comportamento precedente

In precedenza era necessario installare un pacchetto pluralizer separato per plurali DbSet e i nomi di navigazione della raccolta e i nomi di tabella singolari durante l'impalcatura di un DbContext e i tipi di entità per reverse engineering uno schema di database.

#### <a name="new-behavior"></a>Nuovo comportamento

EF Core ora include un pluralizer che usa la libreria [umanizzator](https://github.com/Humanizr/Humanizer) . Si tratta della stessa libreria utilizzata da Visual Studio per consigliare i nomi delle variabili.

#### <a name="why"></a>Perché

L'uso di forme plurali di parole per le proprietà della raccolta e i moduli singolari per i tipi e le proprietà di riferimento è idiomatiche in .NET.

#### <a name="mitigations"></a>Soluzioni di prevenzione

Per disabilitare pluralizer, utilizzare l' `--no-pluralize` opzione on `dotnet ef dbcontext scaffold` o l'opzione `-NoPluralize` on `Scaffold-DbContext` .

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip

[Rilevamento del problema #2568](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>Comportamento precedente

EF Core precedente alla 5,0 supportava solo una forma di proprietà di navigazione, rappresentata dall' `INavigation` interfaccia.

#### <a name="new-behavior"></a>Nuovo comportamento

EF Core 5,0 introduce relazioni molti-a-molti che usano "Ignora spostamento". Sono rappresentati dall' `ISkipNavigation` interfaccia e la maggior parte delle funzionalità di `INavigation` è stata spostata in un'interfaccia di base comune: `INavigationBase` .

#### <a name="why"></a>Perché

La maggior parte delle funzionalità tra le normali e le ignorazioni è la stessa. Tuttavia, ignorare le esplorazioni hanno una relazione diversa con le chiavi esterne rispetto alle normali spostamenti, poiché i FKs interessati non si trovano direttamente su entrambe le estremità della relazione, ma piuttosto nell'entità di join.

#### <a name="mitigations"></a>Soluzioni di prevenzione

In molti casi le applicazioni possono passare all'uso della nuova interfaccia di base senza altre modifiche. Tuttavia, nei casi in cui la navigazione viene utilizzata per accedere alle proprietà di chiave esterna, il codice dell'applicazione deve essere vincolato solo a spostamenti normali oppure è stato aggiornato per eseguire le operazioni appropriate sia per le operazioni di navigazione normale che per quelle ignorate.

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate

[Rilevamento del problema #15873](https://github.com/dotnet/efcore/issues/15873)

**Comportamento precedente**

In precedenza, venivano eseguite query che coinvolgono raccolte correlate seguite da `GroupBy` , nonché alcune query che utilizzano l' `Distinct` autorizzazione.

Esempio di GroupBy:

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

`Distinct` esempio `Distinct` : query specifiche in cui la proiezione della raccolta interna non contiene la chiave primaria:

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

Queste query potrebbero restituire risultati non corretti se la raccolta interna contiene duplicati, ma funziona correttamente se tutti gli elementi della raccolta interna sono univoci.

**Nuovo comportamento**

Queste query non sono più supportate. Viene generata un'eccezione che indica che non sono disponibili informazioni sufficienti per compilare correttamente i risultati.

**Perché**

Per gli scenari di raccolta correlati è necessario individuare la chiave primaria dell'entità per assegnare entità di raccolta al padre corretto. Quando la raccolta interna non usa `GroupBy` o `Distinct` , la chiave primaria mancante può semplicemente essere aggiunta alla proiezione. Tuttavia, nel caso di `GroupBy` e non `Distinct` può essere eseguita perché cambierebbe il risultato dell' `GroupBy` `Distinct` operazione o.

**Soluzioni di prevenzione**

Riscrivere la query in modo da non utilizzare le `GroupBy` `Distinct` operazioni o nella raccolta interna ed eseguire tali operazioni sul client.

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>L'uso di una raccolta di tipi queryable nella proiezione non è supportato

[Rilevamento del problema #16314](https://github.com/dotnet/efcore/issues/16314)

**Comportamento precedente**

In precedenza era possibile usare la raccolta di un tipo Queryable all'interno della proiezione in alcuni casi, ad esempio come argomento di un `List<T>` costruttore:

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**Nuovo comportamento**

Queste query non sono più supportate. Viene generata un'eccezione che indica che non è possibile creare un oggetto di tipo Queryable e suggerire come risolvere il problema.

**Perché**

Non è possibile materializzare un oggetto di un tipo Queryable, in modo che venga creato automaticamente usando il `List<T>` tipo. Questo genererebbe spesso un'eccezione a causa della mancata corrispondenza del tipo che non era molto chiara e potrebbe essere sorprendente per alcuni utenti. Si è deciso di riconoscere il modello e generare un'eccezione più significativa.

**Soluzioni di prevenzione**

Aggiungere una `ToList()` chiamata dopo l'oggetto Queryable nella proiezione:

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
