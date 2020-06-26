---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 0a2ba5b804cc6636b321edcc48feeb76ad60560b
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370370"
---
# <a name="whats-new-in-ef-core-50"></a>Novità di EF Core 5,0

EF Core 5,0 è attualmente in fase di sviluppo. Questa pagina conterrà una panoramica delle modifiche interessanti introdotte in ogni anteprima.

Questa pagina non duplica il [piano per EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan). Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.

I collegamenti da qui vengono aggiunti alla documentazione ufficiale appena pubblicata.

## <a name="preview-6"></a>Preview 6

### <a name="split-queries-for-related-collections"></a>Suddividere le query per le raccolte correlate

A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ. In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso. Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.

EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL. Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query. È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.

#### <a name="split-queries-with-include"></a>Suddividere le query con Includi

Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

La nuova `AsSplitQuery` API può essere usata per modificare questo comportamento. Ad esempio:

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery è disponibile per tutti i provider di database relazionali e può essere usato in qualsiasi punto della query, proprio come AsNoTracking. EF Core genera ora le tre query SQL seguenti:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

Tutte le operazioni nella radice della query sono supportate. Sono incluse le operazioni OrderBy/Skip/Take, le operazioni di join, FirstOrDefault e le operazioni di selezione di un singolo risultato analogo.

Si noti che le inclusioni filtrate con OrderBy/Skip/Take non sono supportate nella versione Preview 6, ma sono disponibili nelle compilazioni giornaliere e saranno incluse nella versione Preview 7.

#### <a name="split-queries-with-collection-projections"></a>Suddividere query con proiezioni di raccolta

`AsSplitQuery`può essere usato anche quando le raccolte vengono caricate nelle proiezioni. Ad esempio:

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Questa query LINQ genera le due query SQL seguenti quando si usa il provider SQLite:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Si noti che è supportata solo la materializzazione della raccolta. Qualsiasi composizione dopo `e.Albums` il caso precedente non comporterà una query Split. I miglioramenti apportati a questa area vengono rilevati da [#21234](https://github.com/dotnet/efcore/issues/21234).

### <a name="indexattribute"></a>IndexAttribute

Il nuovo IndexAttribute può essere inserito in un tipo di entità per specificare un indice per una singola colonna. Ad esempio:

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

Per SQL Server, le migrazioni genereranno la seguente SQL:

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute può essere usato anche per specificare un indice che si estende su più colonne. Ad esempio:

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

Per SQL Server, il risultato è il seguente:

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="improved-query-translation-exceptions"></a>Eccezioni di conversione query migliorate

Si sta continuando a migliorare i messaggi di eccezione generati quando la conversione di query non riesce. Questa query, ad esempio, usa la proprietà non mappata `IsSigned` :

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core genererà l'eccezione seguente indicante che la conversione `IsSigned` non è riuscita perché non è stato eseguito il mapping:

> Eccezione non gestita. System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Non è stato possibile tradurre (a => a. Signed)'. Informazioni aggiuntive: la traduzione del membro ' signed ' nel tipo di entità' Artist ' non è riuscita. Probabilmente il membro specificato non è mappato. Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync (). Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.

Analogamente, vengono generati messaggi di eccezione migliori quando si tenta di tradurre i confronti di stringhe con la semantica dipendente dalle impostazioni cultura. Questa query, ad esempio, tenta di usare `StringComparison.CurrentCulture` :

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core ora verrà generata l'eccezione seguente:

> Eccezione non gestita. System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Dove (a => a. Name. Equals (valore: "unicorni", comparisonType: CurrentCulture))' non può essere convertito. Informazioni aggiuntive: conversione di stringa. Equals ' il metodo che accetta l'argomento ' StringComparison ' non è supportato. Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2129535. Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync (). Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.

### <a name="specify-transaction-id"></a>Specifica ID transazione

Questa funzionalità è stata fornita dalla community da [@Marusyk](https://github.com/Marusyk) . Molti ringraziamenti per il contributo.

EF Core espone un ID transazione per la correlazione delle transazioni tra le chiamate. Questo ID viene in genere impostato da EF Core quando viene avviata una transazione. Se invece l'applicazione avvia la transazione, questa funzionalità consente all'applicazione di impostare in modo esplicito l'ID della transazione in modo che venga correlata correttamente ovunque venga utilizzata. Ad esempio:

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Mapping IPAddress

Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) . Molti ringraziamenti per il contributo.

La [classe IPAddress](/dotnet/api/system.net.ipaddress) .NET standard viene ora mappata automaticamente a una colonna stringa per i database che non dispongono già del supporto nativo. Ad esempio, si consiglia di eseguire il mapping di questo tipo di entità:

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

In SQL Server, le migrazioni comporteranno la creazione della tabella seguente:

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

Le entità possono quindi essere aggiunte in modo normale:

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

E il SQL risultante inserirà l'indirizzo IPv4 o IPv6 normalizzato:

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Escludi onconfigurazione durante l'impalcatura

Quando un DbContext viene sottoposto a impalcatura da un database esistente, per impostazione predefinita EF core crea un overload configurante con una stringa di connessione in modo che il contesto sia immediatamente utilizzabile. Questa operazione non è tuttavia utile se si dispone già di una classe parziale con Configuring oppure se si sta configurando il contesto in altro modo.

Per risolvere questo problema, è ora possibile impostare i comandi di impalcatura per omettere la generazione di onconfigurazione. Ad esempio:

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

In alternativa, nella console di gestione pacchetti:

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

Si consiglia di usare [una stringa di connessione denominata e una risorsa di archiviazione sicura, ad esempio i segreti utente](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Traduzioni per FirstOrDefault in stringhe

Questa funzionalità è stata fornita dalla community da [@dvoreckyaa](https://github.com/dvoreckyaa) . Molti ringraziamenti per il contributo.

FirstOrDefault e operatori simili per i caratteri nelle stringhe sono ora tradotti. Ad esempio, questa query LINQ:

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Verrà convertito nel codice SQL seguente quando si usa SQL Server:

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Semplificare i blocchi di case

EF Core genera ora query migliori con blocchi di maiuscole e minuscole. Ad esempio, questa query LINQ: 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Was on SQL Server formalmente convertita in:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

Ma ora viene convertito in:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>Regole di confronto del database

È ora possibile specificare le regole di confronto predefinite per un database nel modello EF. Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database. Ad esempio:

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Migrations genera quindi quanto segue per creare il database in SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche. Ad esempio:

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.

Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse. Ad esempio:

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

Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto. Ad esempio:

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).

### <a name="no-tracking-queries-with-identity-resolution"></a>Query senza rilevamento con risoluzione di identità

È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità. La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria. 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento. Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata. Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).

### <a name="stored-persisted-computed-columns"></a>Colonne calcolate (rese permanente) archiviate

La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo. Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore. Consente inoltre di indicizzare la colonna per alcuni database.

EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate. Ad esempio:
 
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

È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli. Ad esempio:

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)". 

La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).

### <a name="specify-sql-server-index-fill-factor"></a>Specificare il fattore di riempimento dell'indice SQL Server

È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server. Ad esempio:

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Inclusione filtrato

Il metodo include ora supporta il filtraggio delle entità incluse. Ad esempio:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".

È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse. Ad esempio:
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.

Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nuova API ModelBuilder per le proprietà di navigazione

Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships). Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva. Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Si noti che l' `Navigation` API non sostituisce la configurazione della relazione. Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.

Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione 

Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando. Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi: 

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

Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database. Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.

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

La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query. Ad esempio:

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Supporto per la funzione SQL Server DataLength

È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo. Ad esempio:
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Usare un attributo C# per specificare un campo di supporto delle proprietà

È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà. Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente. Ad esempio:

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

EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](/ef/core/modeling/inheritance). Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore. EF Core 5,0 implementa ora questi miglioramenti.

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

Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6. Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.

La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).

### <a name="simple-way-to-get-generated-sql"></a>Modo semplice per ottenere SQL generato

EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Usare un attributo C# per indicare che un'entità non ha una chiave

È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` . Ad esempio:

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

È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione. Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto. Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.

La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxy di rilevamento delle modifiche

EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) e [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1). Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche. Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.

La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Viste di debug migliorate

Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi. Una visualizzazione di debug per il modello è stata implementata qualche tempo fa. Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.

La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).

### <a name="improved-handling-of-database-null-semantics"></a>Gestione migliorata della semantica null del database

I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL. Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null. EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#. EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.

La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Proprietà indicizzatore

EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#. Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.

La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generazione di vincoli check per i mapping enum

EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione. Ad esempio:

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>Relazionale

`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` . Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale. Ad esempio:

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

Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag. Usare il generatore di modelli in OnModelCreating per configurare un ETag:

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

Le query che utilizzano `Reverse` ora vengono convertite. Ad esempio:

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
