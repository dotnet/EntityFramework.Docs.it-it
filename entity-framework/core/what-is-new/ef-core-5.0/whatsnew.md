---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418942"
---
# <a name="whats-new-in-ef-core-50"></a>Novità di EF Core 5,0

EF Core 5,0 è attualmente in fase di sviluppo.
Questa pagina conterrà una panoramica delle modifiche interessanti introdotte in ogni anteprima.

Questa pagina non duplica il [piano per EF Core 5,0](plan.md).
Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.

I collegamenti da qui vengono aggiunti alla documentazione ufficiale appena pubblicata.

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>Regole di confronto del database

È ora possibile specificare le regole di confronto predefinite per un database nel modello EF.
Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database.
Ad esempio:

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Migrations genera quindi quanto segue per creare il database in SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche.
Ad esempio:

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.

Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse.
Ad esempio:

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

Verrà generata la query seguente per SQL Server:

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Si noti che le regole di confronto ad hoc devono essere utilizzate con cautela, in quanto possono influire negativamente sulle prestazioni del database.

La documentazione viene rilevata in base al problema [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Argomenti del flusso in IDesignTimeDbContextFactory

Gli argomenti vengono ora propagati dalla riga di comando nel `CreateDbContext` metodo di [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1). Per indicare, ad esempio, che si tratta di una build di sviluppo, un argomento personalizzato, ad esempio, `dev` può essere passato dalla riga di comando:

```
dotnet ef migrations add two --verbose --dev
``` 

Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto.
Ad esempio:

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).

### <a name="no-tracking-queries-with-identity-resolution"></a>Query senza rilevamento con risoluzione di identità

È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità.
La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria. 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento. Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata.
Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).

### <a name="stored-persisted-computed-columns"></a>Colonne calcolate (rese permanente) archiviate

La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo.
Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore.
Consente inoltre di indicizzare la colonna per alcuni database.

EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate.
Ad esempio:
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>Colonne calcolate SQLite

EF Core supporta ora le colonne calcolate nei database SQLite.

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>Configurare la precisione del database/ridimensionare il modello

È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli.
Ad esempio:

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)". 

La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).

### <a name="specify-sql-server-index-fill-factor"></a>Specificare il fattore di riempimento dell'indice SQL Server

È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server.
Ad esempio:

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Inclusione filtrato

Il metodo include ora supporta il filtraggio delle entità incluse.
Ad esempio:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".

È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse.
Ad esempio:
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.

Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nuova API ModelBuilder per le proprietà di navigazione

Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships).
Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva.
Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Si noti che l' `Navigation` API non sostituisce la configurazione della relazione.
Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.

Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione 

Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando.
Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi: 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Per informazioni dettagliate, vedere la documentazione sulle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e la [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .

---
Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:

```
dotnet ef database update --connection "connection string"
```

Per informazioni dettagliate, vedere la [documentazione degli strumenti](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .

Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors ha restituito

Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database.
Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.

`EnableDetailedErrors`Se si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.  

Ad esempio:
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).

### <a name="cosmos-partition-keys"></a>Chiavi di partizione Cosmos

La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query.
Ad esempio:

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Supporto per la funzione SQL Server DataLength

È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo.
Ad esempio:
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Usare un attributo C# per specificare un campo di supporto delle proprietà

È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà.
Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente.
Ad esempio:

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).

### <a name="complete-discriminator-mapping"></a>Mapping completo del discriminatore

EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](/ef/core/modeling/inheritance).
Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore.
EF Core 5,0 implementa ora questi miglioramenti.

Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Si tratta del comportamento predefinito che inizia con Preview 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Miglioramenti delle prestazioni in Microsoft. Data. sqlite

Sono stati apportati due miglioramenti alle prestazioni per SQLIte:

* Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.
* L'inizializzazione di SqliteConnection è ora Lazy.

Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>Registrazione semplice

Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6.
Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.

La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).

### <a name="simple-way-to-get-generated-sql"></a>Modo semplice per ottenere SQL generato

EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Usare un attributo C# per indicare che un'entità non ha una chiave

È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` .
Ad esempio:

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>La stringa di connessione o di connessione può essere modificata in DbContext inizializzato

È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione.
Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto.
Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.

La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxy di rilevamento delle modifiche

EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) e [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).
Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche.
Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.

La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Viste di debug migliorate

Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi.
Una visualizzazione di debug per il modello è stata implementata qualche tempo fa.
Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).

### <a name="improved-handling-of-database-null-semantics"></a>Gestione migliorata della semantica null del database

I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL.
Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null.
EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#.
EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.

La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Proprietà indicizzatore

EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#.
Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.

La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generazione di vincoli check per i mapping enum

EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione.
Ad esempio:

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>Relazionale

`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` .
Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale.
Ad esempio:

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Concorrenza ottimistica di Cosmos con ETag

Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag.
Usare il generatore di modelli in OnModelCreating per configurare un ETag:

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges genera quindi un'operazione `DbUpdateConcurrencyException` in un conflitto di concorrenza, che [può essere gestita](https://docs.microsoft.com/ef/core/saving/concurrency) per implementare nuovi tentativi e così via.

La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).

### <a name="query-translations-for-more-datetime-constructs"></a>Eseguire query sulle traduzioni per più costrutti DateTime

Vengono ora convertite le query contenenti nuove costruzioni DateTime.

Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:

* DateDiffWeek
* DateFromParts

Ad esempio:

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translations-for-more-byte-array-constructs"></a>Eseguire query sulle traduzioni per altri costrutti di matrici di byte

Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.

La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-reverse"></a>Conversione di query per invertire

Le query che utilizzano `Reverse` ora vengono convertite.
Ad esempio:

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-bitwise-operators"></a>Conversione di query per operatori bit per bit

Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-strings-on-cosmos"></a>Conversione di query per le stringhe in Cosmos

Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.

La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).
