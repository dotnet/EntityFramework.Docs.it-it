---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103074"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="a9ae1-103">Novità di EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="a9ae1-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="a9ae1-104">EF Core 5,0 è attualmente in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="a9ae1-105">Questa pagina conterrà una panoramica delle modifiche interessanti introdotte in ogni anteprima.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="a9ae1-106">Questa pagina non duplica il [piano per EF Core 5,0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="a9ae1-107">Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="a9ae1-108">I collegamenti da qui vengono aggiunti alla documentazione ufficiale appena pubblicata.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-3"></a><span data-ttu-id="a9ae1-109">Preview 3</span><span class="sxs-lookup"><span data-stu-id="a9ae1-109">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="a9ae1-110">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="a9ae1-110">Filtered Include</span></span>

<span data-ttu-id="a9ae1-111">Il metodo include ora supporta il filtraggio delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-111">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="a9ae1-112">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-112">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="a9ae1-113">Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".</span><span class="sxs-lookup"><span data-stu-id="a9ae1-113">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="a9ae1-114">È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-114">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="a9ae1-115">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-115">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="a9ae1-116">Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-116">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="a9ae1-117">Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="a9ae1-117">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="a9ae1-118">Nuova API ModelBuilder per le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="a9ae1-118">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="a9ae1-119">Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-119">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="a9ae1-120">Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-120">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="a9ae1-121">Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-121">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="a9ae1-122">Si noti che `Navigation` l'API non sostituisce la configurazione della relazione.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-122">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="a9ae1-123">Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-123">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="a9ae1-124">La documentazione viene rilevata in base al problema [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-124">Documentation is tracked by issue [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="a9ae1-125">Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="a9ae1-125">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="a9ae1-126">Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-126">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="a9ae1-127">Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-127">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="a9ae1-128">Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-128">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="a9ae1-129">Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-129">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

<span data-ttu-id="a9ae1-130">La documentazione viene rilevata in base al problema [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-130">Documentation is tracked by issue [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="a9ae1-131">EnableDetailedErrors ha restituito</span><span class="sxs-lookup"><span data-stu-id="a9ae1-131">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="a9ae1-132">Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-132">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="a9ae1-133">Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-133">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="a9ae1-134">Se `EnableDetailedErrors` si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-134">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="a9ae1-135">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-135">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="a9ae1-136">La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-136">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="a9ae1-137">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="a9ae1-137">Cosmos partition keys</span></span>

<span data-ttu-id="a9ae1-138">La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-138">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="a9ae1-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-139">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="a9ae1-140">La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-140">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="a9ae1-141">Supporto per la funzione SQL Server DataLength</span><span class="sxs-lookup"><span data-stu-id="a9ae1-141">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="a9ae1-142">È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-142">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="a9ae1-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-143">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="a9ae1-144">Preview 2</span><span class="sxs-lookup"><span data-stu-id="a9ae1-144">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="a9ae1-145">Usare un attributo C# per specificare un campo di supporto delle proprietà</span><span class="sxs-lookup"><span data-stu-id="a9ae1-145">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="a9ae1-146">È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-146">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="a9ae1-147">Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-147">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="a9ae1-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-148">For example:</span></span>

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

<span data-ttu-id="a9ae1-149">La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-149">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="a9ae1-150">Mapping completo del discriminatore</span><span class="sxs-lookup"><span data-stu-id="a9ae1-150">Complete discriminator mapping</span></span>

<span data-ttu-id="a9ae1-151">EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-151">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="a9ae1-152">Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-152">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="a9ae1-153">EF Core 5,0 implementa ora questi miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-153">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="a9ae1-154">Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-154">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="a9ae1-155">EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-155">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="a9ae1-156">Si tratta del comportamento predefinito che inizia con Preview 3.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-156">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="a9ae1-157">Miglioramenti delle prestazioni in Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="a9ae1-157">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="a9ae1-158">Sono stati apportati due miglioramenti alle prestazioni per SQLIte:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-158">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="a9ae1-159">Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-159">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="a9ae1-160">L'inizializzazione di SqliteConnection è ora Lazy.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-160">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="a9ae1-161">Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-161">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="a9ae1-162">Preview 1</span><span class="sxs-lookup"><span data-stu-id="a9ae1-162">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="a9ae1-163">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="a9ae1-163">Simple logging</span></span>

<span data-ttu-id="a9ae1-164">Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-164">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="a9ae1-165">Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-165">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="a9ae1-166">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-166">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a9ae1-167">La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-167">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="a9ae1-168">Modo semplice per ottenere SQL generato</span><span class="sxs-lookup"><span data-stu-id="a9ae1-168">Simple way to get generated SQL</span></span>

<span data-ttu-id="a9ae1-169">EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-169">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="a9ae1-170">La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-170">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="a9ae1-171">La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-171">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="a9ae1-172">Usare un attributo C# per indicare che un'entità non ha una chiave</span><span class="sxs-lookup"><span data-stu-id="a9ae1-172">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="a9ae1-173">È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-173">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="a9ae1-174">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-174">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="a9ae1-175">La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-175">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="a9ae1-176">La stringa di connessione o di connessione può essere modificata in DbContext inizializzato</span><span class="sxs-lookup"><span data-stu-id="a9ae1-176">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="a9ae1-177">È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-177">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="a9ae1-178">Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-178">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="a9ae1-179">Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-179">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="a9ae1-180">La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-180">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="a9ae1-181">Proxy di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="a9ae1-181">Change-tracking proxies</span></span>

<span data-ttu-id="a9ae1-182">EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) e [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-182">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="a9ae1-183">Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-183">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="a9ae1-184">Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-184">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="a9ae1-185">La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-185">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="a9ae1-186">Viste di debug migliorate</span><span class="sxs-lookup"><span data-stu-id="a9ae1-186">Enhanced debug views</span></span>

<span data-ttu-id="a9ae1-187">Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-187">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="a9ae1-188">Una visualizzazione di debug per il modello è stata implementata qualche tempo fa.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-188">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="a9ae1-189">Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-189">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="a9ae1-190">La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-190">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="a9ae1-191">La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-191">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="a9ae1-192">Gestione migliorata della semantica null del database</span><span class="sxs-lookup"><span data-stu-id="a9ae1-192">Improved handling of database null semantics</span></span>

<span data-ttu-id="a9ae1-193">I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-193">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="a9ae1-194">Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-194">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="a9ae1-195">EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-195">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="a9ae1-196">EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-196">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="a9ae1-197">La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-197">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="a9ae1-198">Proprietà indicizzatore</span><span class="sxs-lookup"><span data-stu-id="a9ae1-198">Indexer properties</span></span>

<span data-ttu-id="a9ae1-199">EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-199">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="a9ae1-200">Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-200">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="a9ae1-201">La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-201">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="a9ae1-202">Generazione di vincoli check per i mapping enum</span><span class="sxs-lookup"><span data-stu-id="a9ae1-202">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="a9ae1-203">EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-203">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="a9ae1-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-204">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="a9ae1-205">La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-205">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="a9ae1-206">Relazionale</span><span class="sxs-lookup"><span data-stu-id="a9ae1-206">IsRelational</span></span>

<span data-ttu-id="a9ae1-207">È stato `IsRelational` aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer`, `IsSqlite`, e `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-207">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="a9ae1-208">Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-208">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="a9ae1-209">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-209">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="a9ae1-210">La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-210">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="a9ae1-211">Concorrenza ottimistica di Cosmos con ETag</span><span class="sxs-lookup"><span data-stu-id="a9ae1-211">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="a9ae1-212">Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-212">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="a9ae1-213">Usare il generatore di modelli in OnModelCreating per configurare un ETag:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-213">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="a9ae1-214">SaveChanges genera quindi un' `DbUpdateConcurrencyException` operazione in un conflitto di concorrenza, che [può essere gestita](https://docs.microsoft.com/ef/core/saving/concurrency) per implementare nuovi tentativi e così via.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-214">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="a9ae1-215">La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-215">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="a9ae1-216">Eseguire query sulle traduzioni per più costrutti DateTime</span><span class="sxs-lookup"><span data-stu-id="a9ae1-216">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="a9ae1-217">Vengono ora convertite le query contenenti nuove costruzioni DateTime.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-217">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="a9ae1-218">Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-218">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="a9ae1-219">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="a9ae1-219">DateDiffWeek</span></span>
* <span data-ttu-id="a9ae1-220">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="a9ae1-220">DateFromParts</span></span>

<span data-ttu-id="a9ae1-221">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-221">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="a9ae1-222">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-222">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="a9ae1-223">Eseguire query sulle traduzioni per altri costrutti di matrici di byte</span><span class="sxs-lookup"><span data-stu-id="a9ae1-223">Query translations for more byte array constructs</span></span>

<span data-ttu-id="a9ae1-224">Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-224">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="a9ae1-225">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-225">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a9ae1-226">La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-226">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="a9ae1-227">Conversione di query per invertire</span><span class="sxs-lookup"><span data-stu-id="a9ae1-227">Query translation for Reverse</span></span>

<span data-ttu-id="a9ae1-228">Le query `Reverse` che utilizzano ora vengono convertite.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-228">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="a9ae1-229">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-229">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="a9ae1-230">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-230">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="a9ae1-231">Conversione di query per operatori bit per bit</span><span class="sxs-lookup"><span data-stu-id="a9ae1-231">Query translation for bitwise operators</span></span>

<span data-ttu-id="a9ae1-232">Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ae1-232">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="a9ae1-233">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="a9ae1-234">Conversione di query per le stringhe in Cosmos</span><span class="sxs-lookup"><span data-stu-id="a9ae1-234">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="a9ae1-235">Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="a9ae1-235">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="a9ae1-236">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a9ae1-236">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
