---
title: Novità di EF Core 5,0
description: Panoramica delle nuove funzionalità di EF Core 5,0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d7f5863e657e243ce733eda5dc8b40c1b92818ce
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526875"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="03b55-103">Novità di EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="03b55-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="03b55-104">EF Core 5,0 è attualmente in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="03b55-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="03b55-105">Questa pagina conterrà una panoramica delle modifiche interessanti introdotte in ogni anteprima.</span><span class="sxs-lookup"><span data-stu-id="03b55-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="03b55-106">Questa pagina non duplica il [piano per EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="03b55-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="03b55-107">Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.</span><span class="sxs-lookup"><span data-stu-id="03b55-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="03b55-108">I collegamenti da qui vengono aggiunti alla documentazione ufficiale appena pubblicata.</span><span class="sxs-lookup"><span data-stu-id="03b55-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="03b55-109">Anteprima 7</span><span class="sxs-lookup"><span data-stu-id="03b55-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="03b55-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="03b55-110">DbContextFactory</span></span>

<span data-ttu-id="03b55-111">EF Core 5,0 introduce `AddDbContextFactory` e `AddPooledDbContextFactory` per registrare una factory per la creazione di istanze di DbContext nel contenitore di inserimento delle dipendenze dell'applicazione (D.I.).</span><span class="sxs-lookup"><span data-stu-id="03b55-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="03b55-112">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="03b55-113">I servizi delle applicazioni come ASP.NET Core controller possono quindi dipendere da `IDbContextFactory<TContext>` nel costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="03b55-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="03b55-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-114">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="03b55-115">Le istanze di DbContext possono quindi essere create e utilizzate in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="03b55-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="03b55-116">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-116">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="03b55-117">Si noti che le istanze di DbContext create in questo modo _non_ sono gestite dal provider di servizi dell'applicazione e pertanto devono essere eliminate dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="03b55-118">Questa separazione è molto utile per le applicazioni blazer, in cui si consiglia l'uso di `IDbContextFactory` , ma può essere utile anche in altri scenari.</span><span class="sxs-lookup"><span data-stu-id="03b55-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="03b55-119">Le istanze di DbContext possono essere raggruppate chiamando `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="03b55-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="03b55-120">Questo pool funziona allo stesso modo di `AddDbContextPool` e presenta anche le stesse limitazioni.</span><span class="sxs-lookup"><span data-stu-id="03b55-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="03b55-121">La documentazione viene rilevata in base al problema [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="03b55-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="03b55-122">Reimposta stato DbContext</span><span class="sxs-lookup"><span data-stu-id="03b55-122">Reset DbContext state</span></span>

<span data-ttu-id="03b55-123">EF Core 5,0 introduce `ChangeTracker.Clear()` che cancella la DbContext di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="03b55-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="03b55-124">Questa situazione in genere non è necessaria quando si usa la procedura consigliata per la creazione di una nuova istanza del contesto di breve durata per ogni unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="03b55-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="03b55-125">Tuttavia, se è necessario reimpostare lo stato di un'istanza di DbContext, l'uso del nuovo `Clear()` metodo è più efficiente e affidabile rispetto alla disconnessione di massa di tutte le entità.</span><span class="sxs-lookup"><span data-stu-id="03b55-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="03b55-126">La documentazione viene rilevata in base al problema [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="03b55-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="03b55-127">Nuovo modello per le impostazioni predefinite generate dall'archivio</span><span class="sxs-lookup"><span data-stu-id="03b55-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="03b55-128">EF Core consente l'impostazione di un valore esplicito per una colonna che può anche avere un vincolo di valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="03b55-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="03b55-129">EF Core usa il valore predefinito CLR del tipo di proprietà Type come sentinella per questo oggetto; Se il valore non è il valore predefinito CLR, viene inserito; in caso contrario, viene utilizzato il valore predefinito del database.</span><span class="sxs-lookup"><span data-stu-id="03b55-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="03b55-130">In questo modo si creano problemi per i tipi in cui il valore predefinito di CLR non è una buona sentinella, in particolare `bool` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="03b55-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="03b55-131">EF Core 5,0 consente ora il campo sottostante come Nullable per i casi di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="03b55-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="03b55-132">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-132">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="03b55-133">Si noti che il campo sottostante ammette i valori null, ma la proprietà esposta pubblicamente non lo è.</span><span class="sxs-lookup"><span data-stu-id="03b55-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="03b55-134">In questo modo, il valore Sentinel può essere `null` senza alcun effetto sulla superficie pubblica del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="03b55-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="03b55-135">In questo caso, se `IsValid` non è mai impostato, verrà usato il valore predefinito del database poiché il campo sottostante rimane null.</span><span class="sxs-lookup"><span data-stu-id="03b55-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="03b55-136">Se `true` o `false` sono impostati, questo valore viene salvato in modo esplicito nel database.</span><span class="sxs-lookup"><span data-stu-id="03b55-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="03b55-137">La documentazione viene rilevata in base al problema [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="03b55-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="03b55-138">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="03b55-138">Cosmos partition keys</span></span>

<span data-ttu-id="03b55-139">EF Core consente di includere la chiave di partizione Cosmos nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="03b55-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="03b55-140">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="03b55-141">A partire dalla versione Preview 7, la chiave di partizione è inclusa nel PK del tipo di entità e viene usata per migliorare le prestazioni in alcune query.</span><span class="sxs-lookup"><span data-stu-id="03b55-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="03b55-142">La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="03b55-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="03b55-143">Configurazione di Cosmos</span><span class="sxs-lookup"><span data-stu-id="03b55-143">Cosmos configuration</span></span>

<span data-ttu-id="03b55-144">EF Core 5,0 migliora la configurazione delle connessioni Cosmos e Cosmos.</span><span class="sxs-lookup"><span data-stu-id="03b55-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="03b55-145">In precedenza EF Core necessario specificare in modo esplicito l'endpoint e la chiave per la connessione a un database Cosmos.</span><span class="sxs-lookup"><span data-stu-id="03b55-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="03b55-146">EF Core 5,0 consente invece di usare una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="03b55-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="03b55-147">Inoltre, EF Core 5,0 consente l'impostazione esplicita dell'istanza WebProxy.</span><span class="sxs-lookup"><span data-stu-id="03b55-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="03b55-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="03b55-149">È ora possibile configurare anche molti altri valori di timeout, limiti e così via.</span><span class="sxs-lookup"><span data-stu-id="03b55-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="03b55-150">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-150">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="03b55-151">Infine, la modalità di connessione predefinita è ora `ConnectionMode.Gateway` , che è in genere più compatibile.</span><span class="sxs-lookup"><span data-stu-id="03b55-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="03b55-152">La documentazione viene rilevata in base al problema [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="03b55-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="03b55-153">Impalcature-DbContext ora singolari</span><span class="sxs-lookup"><span data-stu-id="03b55-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="03b55-154">In precedenza, durante l'impalcatura di un DbContext da un database esistente, EF Core creerà nomi di tipi di entità che corrispondono ai nomi di tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="03b55-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="03b55-155">Le tabelle, ad esempio, hanno `People` `Addresses` generato i tipi di entità denominati `People` e `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="03b55-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="03b55-156">Nelle versioni precedenti questo comportamento era configurabile tramite la registrazione di un servizio di pluralismo.</span><span class="sxs-lookup"><span data-stu-id="03b55-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="03b55-157">Ora, in EF Core 5,0, il pacchetto [umanizzator](https://www.nuget.org/packages/Humanizer.Core/) viene usato come servizio di pluralismo predefinito.</span><span class="sxs-lookup"><span data-stu-id="03b55-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="03b55-158">Ciò significa `People` che le tabelle e `Addresses` verranno ora decodificate in tipi di entità denominati `Person` e `Address` .</span><span class="sxs-lookup"><span data-stu-id="03b55-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="03b55-159">Salvataggio</span><span class="sxs-lookup"><span data-stu-id="03b55-159">Savepoints</span></span>

<span data-ttu-id="03b55-160">EF Core supporta ora [salvataggio](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) per un maggiore controllo sulle transazioni che eseguono più operazioni.</span><span class="sxs-lookup"><span data-stu-id="03b55-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="03b55-161">Salvataggio può essere creato, rilasciato ed eseguito manualmente il rollback.</span><span class="sxs-lookup"><span data-stu-id="03b55-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="03b55-162">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="03b55-163">Inoltre, EF Core ora verrà eseguito il rollback all'ultimo salvataggio quando l'esecuzione `SaveChanges` non riesce.</span><span class="sxs-lookup"><span data-stu-id="03b55-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="03b55-164">Questo consente di ritentare SaveChanges senza ritentare l'intera transazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="03b55-165">La documentazione viene rilevata in base al problema [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="03b55-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="03b55-166">Preview 6</span><span class="sxs-lookup"><span data-stu-id="03b55-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="03b55-167">Suddividere le query per le raccolte correlate</span><span class="sxs-lookup"><span data-stu-id="03b55-167">Split queries for related collections</span></span>

<span data-ttu-id="03b55-168">A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ.</span><span class="sxs-lookup"><span data-stu-id="03b55-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="03b55-169">In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso.</span><span class="sxs-lookup"><span data-stu-id="03b55-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="03b55-170">Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.</span><span class="sxs-lookup"><span data-stu-id="03b55-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="03b55-171">EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="03b55-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="03b55-172">Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query.</span><span class="sxs-lookup"><span data-stu-id="03b55-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="03b55-173">È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.</span><span class="sxs-lookup"><span data-stu-id="03b55-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="03b55-174">Suddividere le query con Includi</span><span class="sxs-lookup"><span data-stu-id="03b55-174">Split queries with Include</span></span>

<span data-ttu-id="03b55-175">Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :</span><span class="sxs-lookup"><span data-stu-id="03b55-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="03b55-176">Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="03b55-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="03b55-177">La nuova `AsSplitQuery` API può essere usata per modificare questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="03b55-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="03b55-178">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="03b55-179">AsSplitQuery è disponibile per tutti i provider di database relazionali e può essere usato in qualsiasi punto della query, proprio come AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="03b55-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="03b55-180">EF Core genera ora le tre query SQL seguenti:</span><span class="sxs-lookup"><span data-stu-id="03b55-180">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="03b55-181">Tutte le operazioni nella radice della query sono supportate.</span><span class="sxs-lookup"><span data-stu-id="03b55-181">All operations on the query root are supported.</span></span> <span data-ttu-id="03b55-182">Sono incluse le operazioni OrderBy/Skip/Take, le operazioni di join, FirstOrDefault e le operazioni di selezione di un singolo risultato analogo.</span><span class="sxs-lookup"><span data-stu-id="03b55-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="03b55-183">Si noti che le inclusioni filtrate con OrderBy/Skip/Take non sono supportate nella versione Preview 6, ma sono disponibili nelle compilazioni giornaliere e saranno incluse nella versione Preview 7.</span><span class="sxs-lookup"><span data-stu-id="03b55-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="03b55-184">Suddividere query con proiezioni di raccolta</span><span class="sxs-lookup"><span data-stu-id="03b55-184">Split queries with collection projections</span></span>

<span data-ttu-id="03b55-185">`AsSplitQuery`può essere usato anche quando le raccolte vengono caricate nelle proiezioni.</span><span class="sxs-lookup"><span data-stu-id="03b55-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="03b55-186">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="03b55-187">Questa query LINQ genera le due query SQL seguenti quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="03b55-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="03b55-188">Si noti che è supportata solo la materializzazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="03b55-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="03b55-189">Qualsiasi composizione dopo `e.Albums` il caso precedente non comporterà una query Split.</span><span class="sxs-lookup"><span data-stu-id="03b55-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="03b55-190">I miglioramenti apportati a questa area vengono rilevati da [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="03b55-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="03b55-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="03b55-191">IndexAttribute</span></span>

<span data-ttu-id="03b55-192">Il nuovo IndexAttribute può essere inserito in un tipo di entità per specificare un indice per una singola colonna.</span><span class="sxs-lookup"><span data-stu-id="03b55-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="03b55-193">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="03b55-194">Per SQL Server, le migrazioni genereranno la seguente SQL:</span><span class="sxs-lookup"><span data-stu-id="03b55-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="03b55-195">IndexAttribute può essere usato anche per specificare un indice che si estende su più colonne.</span><span class="sxs-lookup"><span data-stu-id="03b55-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="03b55-196">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-196">For example:</span></span>

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

<span data-ttu-id="03b55-197">Per SQL Server, il risultato è il seguente:</span><span class="sxs-lookup"><span data-stu-id="03b55-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="03b55-198">La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="03b55-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="03b55-199">Eccezioni di conversione query migliorate</span><span class="sxs-lookup"><span data-stu-id="03b55-199">Improved query translation exceptions</span></span>

<span data-ttu-id="03b55-200">Si sta continuando a migliorare i messaggi di eccezione generati quando la conversione di query non riesce.</span><span class="sxs-lookup"><span data-stu-id="03b55-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="03b55-201">Questa query, ad esempio, usa la proprietà non mappata `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="03b55-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="03b55-202">EF Core genererà l'eccezione seguente indicante che la conversione `IsSigned` non è riuscita perché non è stato eseguito il mapping:</span><span class="sxs-lookup"><span data-stu-id="03b55-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="03b55-203">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="03b55-203">Unhandled exception.</span></span> <span data-ttu-id="03b55-204">System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Non è stato possibile tradurre (a => a. Signed)'.</span><span class="sxs-lookup"><span data-stu-id="03b55-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="03b55-205">Informazioni aggiuntive: la traduzione del membro ' signed ' nel tipo di entità' Artist ' non è riuscita.</span><span class="sxs-lookup"><span data-stu-id="03b55-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="03b55-206">Probabilmente il membro specificato non è mappato.</span><span class="sxs-lookup"><span data-stu-id="03b55-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="03b55-207">Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="03b55-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="03b55-208">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="03b55-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="03b55-209">Analogamente, vengono generati messaggi di eccezione migliori quando si tenta di tradurre i confronti di stringhe con la semantica dipendente dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="03b55-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="03b55-210">Questa query, ad esempio, tenta di usare `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="03b55-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="03b55-211">EF Core ora verrà generata l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="03b55-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="03b55-212">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="03b55-212">Unhandled exception.</span></span> <span data-ttu-id="03b55-213">System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Dove (a => a. Name. Equals (valore: "unicorni", comparisonType: CurrentCulture))' non può essere convertito.</span><span class="sxs-lookup"><span data-stu-id="03b55-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="03b55-214">Informazioni aggiuntive: conversione di stringa. Equals ' il metodo che accetta l'argomento ' StringComparison ' non è supportato.</span><span class="sxs-lookup"><span data-stu-id="03b55-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="03b55-215">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="03b55-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="03b55-216">Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="03b55-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="03b55-217">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="03b55-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="03b55-218">Specifica ID transazione</span><span class="sxs-lookup"><span data-stu-id="03b55-218">Specify transaction ID</span></span>

<span data-ttu-id="03b55-219">Questa funzionalità è stata fornita dalla community da [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="03b55-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="03b55-220">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="03b55-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="03b55-221">EF Core espone un ID transazione per la correlazione delle transazioni tra le chiamate.</span><span class="sxs-lookup"><span data-stu-id="03b55-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="03b55-222">Questo ID viene in genere impostato da EF Core quando viene avviata una transazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="03b55-223">Se invece l'applicazione avvia la transazione, questa funzionalità consente all'applicazione di impostare in modo esplicito l'ID della transazione in modo che venga correlata correttamente ovunque venga utilizzata.</span><span class="sxs-lookup"><span data-stu-id="03b55-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="03b55-224">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="03b55-225">Mapping IPAddress</span><span class="sxs-lookup"><span data-stu-id="03b55-225">IPAddress mapping</span></span>

<span data-ttu-id="03b55-226">Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="03b55-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="03b55-227">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="03b55-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="03b55-228">La [classe IPAddress](/dotnet/api/system.net.ipaddress) .NET standard viene ora mappata automaticamente a una colonna stringa per i database che non dispongono già del supporto nativo.</span><span class="sxs-lookup"><span data-stu-id="03b55-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="03b55-229">Ad esempio, si consiglia di eseguire il mapping di questo tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="03b55-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="03b55-230">In SQL Server, le migrazioni comporteranno la creazione della tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="03b55-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="03b55-231">Le entità possono quindi essere aggiunte in modo normale:</span><span class="sxs-lookup"><span data-stu-id="03b55-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="03b55-232">E il SQL risultante inserirà l'indirizzo IPv4 o IPv6 normalizzato:</span><span class="sxs-lookup"><span data-stu-id="03b55-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="03b55-233">Escludi onconfigurazione durante l'impalcatura</span><span class="sxs-lookup"><span data-stu-id="03b55-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="03b55-234">Quando un DbContext viene sottoposto a impalcatura da un database esistente, per impostazione predefinita EF core crea un overload configurante con una stringa di connessione in modo che il contesto sia immediatamente utilizzabile.</span><span class="sxs-lookup"><span data-stu-id="03b55-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="03b55-235">Questa operazione non è tuttavia utile se si dispone già di una classe parziale con Configuring oppure se si sta configurando il contesto in altro modo.</span><span class="sxs-lookup"><span data-stu-id="03b55-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="03b55-236">Per risolvere questo problema, è ora possibile impostare i comandi di impalcatura per omettere la generazione di onconfigurazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="03b55-237">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="03b55-238">In alternativa, nella console di gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="03b55-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="03b55-239">Si consiglia di usare [una stringa di connessione denominata e una risorsa di archiviazione sicura, ad esempio i segreti utente](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="03b55-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="03b55-240">Traduzioni per FirstOrDefault in stringhe</span><span class="sxs-lookup"><span data-stu-id="03b55-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="03b55-241">Questa funzionalità è stata fornita dalla community da [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="03b55-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="03b55-242">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="03b55-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="03b55-243">FirstOrDefault e operatori simili per i caratteri nelle stringhe sono ora tradotti.</span><span class="sxs-lookup"><span data-stu-id="03b55-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="03b55-244">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="03b55-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="03b55-245">Verrà convertito nel codice SQL seguente quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="03b55-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="03b55-246">Semplificare i blocchi di case</span><span class="sxs-lookup"><span data-stu-id="03b55-246">Simplify case blocks</span></span>

<span data-ttu-id="03b55-247">EF Core genera ora query migliori con blocchi di maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="03b55-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="03b55-248">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="03b55-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="03b55-249">Was on SQL Server formalmente convertita in:</span><span class="sxs-lookup"><span data-stu-id="03b55-249">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="03b55-250">Ma ora viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="03b55-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="03b55-251">Preview 5</span><span class="sxs-lookup"><span data-stu-id="03b55-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="03b55-252">Regole di confronto del database</span><span class="sxs-lookup"><span data-stu-id="03b55-252">Database collations</span></span>

<span data-ttu-id="03b55-253">È ora possibile specificare le regole di confronto predefinite per un database nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="03b55-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="03b55-254">Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database.</span><span class="sxs-lookup"><span data-stu-id="03b55-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="03b55-255">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="03b55-256">Migrations genera quindi quanto segue per creare il database in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="03b55-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="03b55-257">È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche.</span><span class="sxs-lookup"><span data-stu-id="03b55-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="03b55-258">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="03b55-259">Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.</span><span class="sxs-lookup"><span data-stu-id="03b55-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="03b55-260">Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse.</span><span class="sxs-lookup"><span data-stu-id="03b55-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="03b55-261">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="03b55-262">Verrà generata la query seguente per SQL Server:</span><span class="sxs-lookup"><span data-stu-id="03b55-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="03b55-263">Si noti che le regole di confronto ad hoc devono essere utilizzate con cautela, in quanto possono influire negativamente sulle prestazioni del database.</span><span class="sxs-lookup"><span data-stu-id="03b55-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="03b55-264">La documentazione viene rilevata in base al problema [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="03b55-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="03b55-265">Argomenti del flusso in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="03b55-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="03b55-266">Gli argomenti vengono ora propagati dalla riga di comando nel `CreateDbContext` metodo di [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="03b55-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="03b55-267">Per indicare, ad esempio, che si tratta di una build di sviluppo, è possibile passare un argomento personalizzato, ad esempio, `dev` nella riga di comando:</span><span class="sxs-lookup"><span data-stu-id="03b55-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="03b55-268">Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto.</span><span class="sxs-lookup"><span data-stu-id="03b55-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="03b55-269">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="03b55-270">La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="03b55-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="03b55-271">Query senza rilevamento con risoluzione di identità</span><span class="sxs-lookup"><span data-stu-id="03b55-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="03b55-272">È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità.</span><span class="sxs-lookup"><span data-stu-id="03b55-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="03b55-273">La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="03b55-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="03b55-274">Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:</span><span class="sxs-lookup"><span data-stu-id="03b55-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="03b55-275">Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="03b55-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="03b55-276">Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata.</span><span class="sxs-lookup"><span data-stu-id="03b55-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="03b55-277">Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="03b55-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="03b55-278">La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="03b55-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="03b55-279">Colonne calcolate (rese permanente) archiviate</span><span class="sxs-lookup"><span data-stu-id="03b55-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="03b55-280">La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo.</span><span class="sxs-lookup"><span data-stu-id="03b55-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="03b55-281">Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore.</span><span class="sxs-lookup"><span data-stu-id="03b55-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="03b55-282">Consente inoltre di indicizzare la colonna per alcuni database.</span><span class="sxs-lookup"><span data-stu-id="03b55-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="03b55-283">EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate.</span><span class="sxs-lookup"><span data-stu-id="03b55-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="03b55-284">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="03b55-285">Colonne calcolate SQLite</span><span class="sxs-lookup"><span data-stu-id="03b55-285">SQLite computed columns</span></span>

<span data-ttu-id="03b55-286">EF Core supporta ora le colonne calcolate nei database SQLite.</span><span class="sxs-lookup"><span data-stu-id="03b55-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="03b55-287">Preview 4</span><span class="sxs-lookup"><span data-stu-id="03b55-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="03b55-288">Configurare la precisione del database/ridimensionare il modello</span><span class="sxs-lookup"><span data-stu-id="03b55-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="03b55-289">È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli.</span><span class="sxs-lookup"><span data-stu-id="03b55-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="03b55-290">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="03b55-291">La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="03b55-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="03b55-292">La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="03b55-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="03b55-293">Specificare il fattore di riempimento dell'indice SQL Server</span><span class="sxs-lookup"><span data-stu-id="03b55-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="03b55-294">È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="03b55-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="03b55-295">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="03b55-296">Preview 3</span><span class="sxs-lookup"><span data-stu-id="03b55-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="03b55-297">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="03b55-297">Filtered Include</span></span>

<span data-ttu-id="03b55-298">Il metodo include ora supporta il filtraggio delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="03b55-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="03b55-299">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="03b55-300">Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".</span><span class="sxs-lookup"><span data-stu-id="03b55-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="03b55-301">È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse.</span><span class="sxs-lookup"><span data-stu-id="03b55-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="03b55-302">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="03b55-303">Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.</span><span class="sxs-lookup"><span data-stu-id="03b55-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="03b55-304">Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="03b55-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="03b55-305">Nuova API ModelBuilder per le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="03b55-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="03b55-306">Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="03b55-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="03b55-307">Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="03b55-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="03b55-308">Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:</span><span class="sxs-lookup"><span data-stu-id="03b55-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="03b55-309">Si noti che l' `Navigation` API non sostituisce la configurazione della relazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="03b55-310">Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.</span><span class="sxs-lookup"><span data-stu-id="03b55-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="03b55-311">Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="03b55-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="03b55-312">Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="03b55-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="03b55-313">Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando.</span><span class="sxs-lookup"><span data-stu-id="03b55-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="03b55-314">Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi:</span><span class="sxs-lookup"><span data-stu-id="03b55-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="03b55-315">Per informazioni dettagliate, vedere la documentazione sulle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e la [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="03b55-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="03b55-316">Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:</span><span class="sxs-lookup"><span data-stu-id="03b55-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="03b55-317">Per informazioni dettagliate, vedere la [documentazione degli strumenti](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="03b55-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="03b55-318">Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03b55-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="03b55-319">EnableDetailedErrors ha restituito</span><span class="sxs-lookup"><span data-stu-id="03b55-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="03b55-320">Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database.</span><span class="sxs-lookup"><span data-stu-id="03b55-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="03b55-321">Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.</span><span class="sxs-lookup"><span data-stu-id="03b55-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="03b55-322">`EnableDetailedErrors`Se si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.</span><span class="sxs-lookup"><span data-stu-id="03b55-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="03b55-323">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="03b55-324">La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="03b55-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="03b55-325">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="03b55-325">Cosmos partition keys</span></span>

<span data-ttu-id="03b55-326">La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query.</span><span class="sxs-lookup"><span data-stu-id="03b55-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="03b55-327">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="03b55-328">La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="03b55-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="03b55-329">Supporto per la funzione SQL Server DataLength</span><span class="sxs-lookup"><span data-stu-id="03b55-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="03b55-330">È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo.</span><span class="sxs-lookup"><span data-stu-id="03b55-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="03b55-331">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="03b55-332">Preview 2</span><span class="sxs-lookup"><span data-stu-id="03b55-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="03b55-333">Usare un attributo C# per specificare un campo di supporto delle proprietà</span><span class="sxs-lookup"><span data-stu-id="03b55-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="03b55-334">È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà.</span><span class="sxs-lookup"><span data-stu-id="03b55-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="03b55-335">Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="03b55-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="03b55-336">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-336">For example:</span></span>

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

<span data-ttu-id="03b55-337">La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="03b55-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="03b55-338">Mapping completo del discriminatore</span><span class="sxs-lookup"><span data-stu-id="03b55-338">Complete discriminator mapping</span></span>

<span data-ttu-id="03b55-339">EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="03b55-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="03b55-340">Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore.</span><span class="sxs-lookup"><span data-stu-id="03b55-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="03b55-341">EF Core 5,0 implementa ora questi miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="03b55-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="03b55-342">Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:</span><span class="sxs-lookup"><span data-stu-id="03b55-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="03b55-343">EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:</span><span class="sxs-lookup"><span data-stu-id="03b55-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="03b55-344">Si tratta del comportamento predefinito che inizia con Preview 3.</span><span class="sxs-lookup"><span data-stu-id="03b55-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="03b55-345">Miglioramenti delle prestazioni in Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="03b55-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="03b55-346">Sono stati apportati due miglioramenti alle prestazioni per SQLIte:</span><span class="sxs-lookup"><span data-stu-id="03b55-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="03b55-347">Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.</span><span class="sxs-lookup"><span data-stu-id="03b55-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="03b55-348">L'inizializzazione di SqliteConnection è ora Lazy.</span><span class="sxs-lookup"><span data-stu-id="03b55-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="03b55-349">Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.</span><span class="sxs-lookup"><span data-stu-id="03b55-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="03b55-350">Preview 1</span><span class="sxs-lookup"><span data-stu-id="03b55-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="03b55-351">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="03b55-351">Simple logging</span></span>

<span data-ttu-id="03b55-352">Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6.</span><span class="sxs-lookup"><span data-stu-id="03b55-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="03b55-353">Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.</span><span class="sxs-lookup"><span data-stu-id="03b55-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="03b55-354">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="03b55-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="03b55-355">La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="03b55-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="03b55-356">Modo semplice per ottenere SQL generato</span><span class="sxs-lookup"><span data-stu-id="03b55-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="03b55-357">EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="03b55-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="03b55-358">La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="03b55-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="03b55-359">La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="03b55-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="03b55-360">Usare un attributo C# per indicare che un'entità non ha una chiave</span><span class="sxs-lookup"><span data-stu-id="03b55-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="03b55-361">È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="03b55-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="03b55-362">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="03b55-363">La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="03b55-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="03b55-364">La stringa di connessione o di connessione può essere modificata in DbContext inizializzato</span><span class="sxs-lookup"><span data-stu-id="03b55-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="03b55-365">È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="03b55-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="03b55-366">Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="03b55-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="03b55-367">Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.</span><span class="sxs-lookup"><span data-stu-id="03b55-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="03b55-368">La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="03b55-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="03b55-369">Proxy di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="03b55-369">Change-tracking proxies</span></span>

<span data-ttu-id="03b55-370">EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) e [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="03b55-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="03b55-371">Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="03b55-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="03b55-372">Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.</span><span class="sxs-lookup"><span data-stu-id="03b55-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="03b55-373">La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="03b55-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="03b55-374">Viste di debug migliorate</span><span class="sxs-lookup"><span data-stu-id="03b55-374">Enhanced debug views</span></span>

<span data-ttu-id="03b55-375">Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi.</span><span class="sxs-lookup"><span data-stu-id="03b55-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="03b55-376">Una visualizzazione di debug per il modello è stata implementata qualche tempo fa.</span><span class="sxs-lookup"><span data-stu-id="03b55-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="03b55-377">Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.</span><span class="sxs-lookup"><span data-stu-id="03b55-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="03b55-378">La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="03b55-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="03b55-379">La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="03b55-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="03b55-380">Gestione migliorata della semantica null del database</span><span class="sxs-lookup"><span data-stu-id="03b55-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="03b55-381">I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL.</span><span class="sxs-lookup"><span data-stu-id="03b55-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="03b55-382">Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null.</span><span class="sxs-lookup"><span data-stu-id="03b55-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="03b55-383">EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#.</span><span class="sxs-lookup"><span data-stu-id="03b55-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="03b55-384">EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.</span><span class="sxs-lookup"><span data-stu-id="03b55-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="03b55-385">La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="03b55-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="03b55-386">Proprietà indicizzatore</span><span class="sxs-lookup"><span data-stu-id="03b55-386">Indexer properties</span></span>

<span data-ttu-id="03b55-387">EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#.</span><span class="sxs-lookup"><span data-stu-id="03b55-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="03b55-388">Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="03b55-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="03b55-389">La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="03b55-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="03b55-390">Generazione di vincoli check per i mapping enum</span><span class="sxs-lookup"><span data-stu-id="03b55-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="03b55-391">EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="03b55-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="03b55-392">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="03b55-393">La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="03b55-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="03b55-394">Relazionale</span><span class="sxs-lookup"><span data-stu-id="03b55-394">IsRelational</span></span>

<span data-ttu-id="03b55-395">`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="03b55-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="03b55-396">Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale.</span><span class="sxs-lookup"><span data-stu-id="03b55-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="03b55-397">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="03b55-398">La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="03b55-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="03b55-399">Concorrenza ottimistica di Cosmos con ETag</span><span class="sxs-lookup"><span data-stu-id="03b55-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="03b55-400">Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag.</span><span class="sxs-lookup"><span data-stu-id="03b55-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="03b55-401">Usare il generatore di modelli in OnModelCreating per configurare un ETag:</span><span class="sxs-lookup"><span data-stu-id="03b55-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="03b55-402">SaveChanges genera quindi un'operazione `DbUpdateConcurrencyException` in un conflitto di concorrenza, che [può essere gestita](/ef/core/saving/concurrency) per implementare nuovi tentativi e così via.</span><span class="sxs-lookup"><span data-stu-id="03b55-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="03b55-403">La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="03b55-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="03b55-404">Eseguire query sulle traduzioni per più costrutti DateTime</span><span class="sxs-lookup"><span data-stu-id="03b55-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="03b55-405">Vengono ora convertite le query contenenti nuove costruzioni DateTime.</span><span class="sxs-lookup"><span data-stu-id="03b55-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="03b55-406">Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:</span><span class="sxs-lookup"><span data-stu-id="03b55-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="03b55-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="03b55-407">DateDiffWeek</span></span>
* <span data-ttu-id="03b55-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="03b55-408">DateFromParts</span></span>

<span data-ttu-id="03b55-409">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="03b55-410">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="03b55-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="03b55-411">Eseguire query sulle traduzioni per altri costrutti di matrici di byte</span><span class="sxs-lookup"><span data-stu-id="03b55-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="03b55-412">Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.</span><span class="sxs-lookup"><span data-stu-id="03b55-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="03b55-413">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="03b55-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="03b55-414">La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="03b55-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="03b55-415">Conversione di query per invertire</span><span class="sxs-lookup"><span data-stu-id="03b55-415">Query translation for Reverse</span></span>

<span data-ttu-id="03b55-416">Le query che utilizzano `Reverse` ora vengono convertite.</span><span class="sxs-lookup"><span data-stu-id="03b55-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="03b55-417">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="03b55-418">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="03b55-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="03b55-419">Conversione di query per operatori bit per bit</span><span class="sxs-lookup"><span data-stu-id="03b55-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="03b55-420">Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="03b55-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="03b55-421">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="03b55-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="03b55-422">Conversione di query per le stringhe in Cosmos</span><span class="sxs-lookup"><span data-stu-id="03b55-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="03b55-423">Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="03b55-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="03b55-424">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="03b55-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
