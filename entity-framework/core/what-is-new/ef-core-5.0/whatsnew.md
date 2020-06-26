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
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="7a583-103">Novità di EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="7a583-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="7a583-104">EF Core 5,0 è attualmente in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="7a583-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="7a583-105">Questa pagina conterrà una panoramica delle modifiche interessanti introdotte in ogni anteprima.</span><span class="sxs-lookup"><span data-stu-id="7a583-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="7a583-106">Questa pagina non duplica il [piano per EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="7a583-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="7a583-107">Il piano descrive i temi generali per EF Core 5,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.</span><span class="sxs-lookup"><span data-stu-id="7a583-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="7a583-108">I collegamenti da qui vengono aggiunti alla documentazione ufficiale appena pubblicata.</span><span class="sxs-lookup"><span data-stu-id="7a583-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-6"></a><span data-ttu-id="7a583-109">Preview 6</span><span class="sxs-lookup"><span data-stu-id="7a583-109">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="7a583-110">Suddividere le query per le raccolte correlate</span><span class="sxs-lookup"><span data-stu-id="7a583-110">Split queries for related collections</span></span>

<span data-ttu-id="7a583-111">A partire da EF Core 3,0, EF Core genera sempre una singola query SQL per ogni query LINQ.</span><span class="sxs-lookup"><span data-stu-id="7a583-111">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="7a583-112">In questo modo si garantisce la coerenza dei dati restituiti nei vincoli della modalità di transazione in uso.</span><span class="sxs-lookup"><span data-stu-id="7a583-112">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="7a583-113">Questa operazione può tuttavia risultare molto lenta quando la query USA `Include` o una proiezione per ripristinare più raccolte correlate.</span><span class="sxs-lookup"><span data-stu-id="7a583-113">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="7a583-114">EF Core 5,0 consente ora una singola query LINQ, incluse le raccolte correlate, che possono essere suddivise in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="7a583-114">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="7a583-115">Questo può migliorare significativamente le prestazioni, ma può comportare un'incoerenza nei risultati restituiti se i dati vengono modificati tra le due query.</span><span class="sxs-lookup"><span data-stu-id="7a583-115">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="7a583-116">È possibile utilizzare le transazioni serializzabili o snapshot per attenuare questo problema e ottenere la coerenza con le query suddivise, ma ciò può comportare altri costi di prestazioni e differenze di comportamento.</span><span class="sxs-lookup"><span data-stu-id="7a583-116">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="7a583-117">Suddividere le query con Includi</span><span class="sxs-lookup"><span data-stu-id="7a583-117">Split queries with Include</span></span>

<span data-ttu-id="7a583-118">Si consideri, ad esempio, una query che esegue il pull di due livelli di raccolte correlate utilizzando `Include` :</span><span class="sxs-lookup"><span data-stu-id="7a583-118">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7a583-119">Per impostazione predefinita, EF Core genererà il codice SQL seguente quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="7a583-119">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="7a583-120">La nuova `AsSplitQuery` API può essere usata per modificare questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="7a583-120">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="7a583-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-121">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7a583-122">AsSplitQuery è disponibile per tutti i provider di database relazionali e può essere usato in qualsiasi punto della query, proprio come AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="7a583-122">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="7a583-123">EF Core genera ora le tre query SQL seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a583-123">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="7a583-124">Tutte le operazioni nella radice della query sono supportate.</span><span class="sxs-lookup"><span data-stu-id="7a583-124">All operations on the query root are supported.</span></span> <span data-ttu-id="7a583-125">Sono incluse le operazioni OrderBy/Skip/Take, le operazioni di join, FirstOrDefault e le operazioni di selezione di un singolo risultato analogo.</span><span class="sxs-lookup"><span data-stu-id="7a583-125">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="7a583-126">Si noti che le inclusioni filtrate con OrderBy/Skip/Take non sono supportate nella versione Preview 6, ma sono disponibili nelle compilazioni giornaliere e saranno incluse nella versione Preview 7.</span><span class="sxs-lookup"><span data-stu-id="7a583-126">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="7a583-127">Suddividere query con proiezioni di raccolta</span><span class="sxs-lookup"><span data-stu-id="7a583-127">Split queries with collection projections</span></span>

<span data-ttu-id="7a583-128">`AsSplitQuery`può essere usato anche quando le raccolte vengono caricate nelle proiezioni.</span><span class="sxs-lookup"><span data-stu-id="7a583-128">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="7a583-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-129">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="7a583-130">Questa query LINQ genera le due query SQL seguenti quando si usa il provider SQLite:</span><span class="sxs-lookup"><span data-stu-id="7a583-130">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="7a583-131">Si noti che è supportata solo la materializzazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="7a583-131">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="7a583-132">Qualsiasi composizione dopo `e.Albums` il caso precedente non comporterà una query Split.</span><span class="sxs-lookup"><span data-stu-id="7a583-132">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="7a583-133">I miglioramenti apportati a questa area vengono rilevati da [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="7a583-133">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="7a583-134">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="7a583-134">IndexAttribute</span></span>

<span data-ttu-id="7a583-135">Il nuovo IndexAttribute può essere inserito in un tipo di entità per specificare un indice per una singola colonna.</span><span class="sxs-lookup"><span data-stu-id="7a583-135">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="7a583-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-136">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="7a583-137">Per SQL Server, le migrazioni genereranno la seguente SQL:</span><span class="sxs-lookup"><span data-stu-id="7a583-137">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="7a583-138">IndexAttribute può essere usato anche per specificare un indice che si estende su più colonne.</span><span class="sxs-lookup"><span data-stu-id="7a583-138">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="7a583-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-139">For example:</span></span>

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

<span data-ttu-id="7a583-140">Per SQL Server, il risultato è il seguente:</span><span class="sxs-lookup"><span data-stu-id="7a583-140">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="7a583-141">La documentazione viene rilevata in base al problema [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="7a583-141">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="7a583-142">Eccezioni di conversione query migliorate</span><span class="sxs-lookup"><span data-stu-id="7a583-142">Improved query translation exceptions</span></span>

<span data-ttu-id="7a583-143">Si sta continuando a migliorare i messaggi di eccezione generati quando la conversione di query non riesce.</span><span class="sxs-lookup"><span data-stu-id="7a583-143">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="7a583-144">Questa query, ad esempio, usa la proprietà non mappata `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="7a583-144">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="7a583-145">EF Core genererà l'eccezione seguente indicante che la conversione `IsSigned` non è riuscita perché non è stato eseguito il mapping:</span><span class="sxs-lookup"><span data-stu-id="7a583-145">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="7a583-146">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="7a583-146">Unhandled exception.</span></span> <span data-ttu-id="7a583-147">System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Non è stato possibile tradurre (a => a. Signed)'.</span><span class="sxs-lookup"><span data-stu-id="7a583-147">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="7a583-148">Informazioni aggiuntive: la traduzione del membro ' signed ' nel tipo di entità' Artist ' non è riuscita.</span><span class="sxs-lookup"><span data-stu-id="7a583-148">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="7a583-149">Probabilmente il membro specificato non è mappato.</span><span class="sxs-lookup"><span data-stu-id="7a583-149">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="7a583-150">Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="7a583-150">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="7a583-151">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="7a583-151">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="7a583-152">Analogamente, vengono generati messaggi di eccezione migliori quando si tenta di tradurre i confronti di stringhe con la semantica dipendente dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="7a583-152">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="7a583-153">Questa query, ad esempio, tenta di usare `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="7a583-153">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="7a583-154">EF Core ora verrà generata l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="7a583-154">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="7a583-155">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="7a583-155">Unhandled exception.</span></span> <span data-ttu-id="7a583-156">System. InvalidOperationException: espressione LINQ ' DbSet <Artist> (). Dove (a => a. Name. Equals (valore: "unicorni", comparisonType: CurrentCulture))' non può essere convertito.</span><span class="sxs-lookup"><span data-stu-id="7a583-156">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="7a583-157">Informazioni aggiuntive: conversione di stringa. Equals ' il metodo che accetta l'argomento ' StringComparison ' non è supportato.</span><span class="sxs-lookup"><span data-stu-id="7a583-157">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="7a583-158">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="7a583-158">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="7a583-159">Riscrivere la query in un modulo che può essere convertito oppure passare alla valutazione client in modo esplicito inserendo una chiamata a AsEnumerable (), AsAsyncEnumerable (), ToList () o ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="7a583-159">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="7a583-160">Per altre informazioni, vedere https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="7a583-160">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="7a583-161">Specifica ID transazione</span><span class="sxs-lookup"><span data-stu-id="7a583-161">Specify transaction ID</span></span>

<span data-ttu-id="7a583-162">Questa funzionalità è stata fornita dalla community da [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="7a583-162">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="7a583-163">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="7a583-163">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a583-164">EF Core espone un ID transazione per la correlazione delle transazioni tra le chiamate.</span><span class="sxs-lookup"><span data-stu-id="7a583-164">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="7a583-165">Questo ID viene in genere impostato da EF Core quando viene avviata una transazione.</span><span class="sxs-lookup"><span data-stu-id="7a583-165">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="7a583-166">Se invece l'applicazione avvia la transazione, questa funzionalità consente all'applicazione di impostare in modo esplicito l'ID della transazione in modo che venga correlata correttamente ovunque venga utilizzata.</span><span class="sxs-lookup"><span data-stu-id="7a583-166">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="7a583-167">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-167">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="7a583-168">Mapping IPAddress</span><span class="sxs-lookup"><span data-stu-id="7a583-168">IPAddress mapping</span></span>

<span data-ttu-id="7a583-169">Questa funzionalità è stata fornita dalla community da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="7a583-169">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="7a583-170">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="7a583-170">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a583-171">La [classe IPAddress](/dotnet/api/system.net.ipaddress) .NET standard viene ora mappata automaticamente a una colonna stringa per i database che non dispongono già del supporto nativo.</span><span class="sxs-lookup"><span data-stu-id="7a583-171">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="7a583-172">Ad esempio, si consiglia di eseguire il mapping di questo tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="7a583-172">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="7a583-173">In SQL Server, le migrazioni comporteranno la creazione della tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="7a583-173">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="7a583-174">Le entità possono quindi essere aggiunte in modo normale:</span><span class="sxs-lookup"><span data-stu-id="7a583-174">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="7a583-175">E il SQL risultante inserirà l'indirizzo IPv4 o IPv6 normalizzato:</span><span class="sxs-lookup"><span data-stu-id="7a583-175">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="7a583-176">Escludi onconfigurazione durante l'impalcatura</span><span class="sxs-lookup"><span data-stu-id="7a583-176">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="7a583-177">Quando un DbContext viene sottoposto a impalcatura da un database esistente, per impostazione predefinita EF core crea un overload configurante con una stringa di connessione in modo che il contesto sia immediatamente utilizzabile.</span><span class="sxs-lookup"><span data-stu-id="7a583-177">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="7a583-178">Questa operazione non è tuttavia utile se si dispone già di una classe parziale con Configuring oppure se si sta configurando il contesto in altro modo.</span><span class="sxs-lookup"><span data-stu-id="7a583-178">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="7a583-179">Per risolvere questo problema, è ora possibile impostare i comandi di impalcatura per omettere la generazione di onconfigurazione.</span><span class="sxs-lookup"><span data-stu-id="7a583-179">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="7a583-180">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-180">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="7a583-181">In alternativa, nella console di gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="7a583-181">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="7a583-182">Si consiglia di usare [una stringa di connessione denominata e una risorsa di archiviazione sicura, ad esempio i segreti utente](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="7a583-182">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="7a583-183">Traduzioni per FirstOrDefault in stringhe</span><span class="sxs-lookup"><span data-stu-id="7a583-183">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="7a583-184">Questa funzionalità è stata fornita dalla community da [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="7a583-184">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="7a583-185">Molti ringraziamenti per il contributo.</span><span class="sxs-lookup"><span data-stu-id="7a583-185">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a583-186">FirstOrDefault e operatori simili per i caratteri nelle stringhe sono ora tradotti.</span><span class="sxs-lookup"><span data-stu-id="7a583-186">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="7a583-187">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="7a583-187">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="7a583-188">Verrà convertito nel codice SQL seguente quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a583-188">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="7a583-189">Semplificare i blocchi di case</span><span class="sxs-lookup"><span data-stu-id="7a583-189">Simplify case blocks</span></span>

<span data-ttu-id="7a583-190">EF Core genera ora query migliori con blocchi di maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="7a583-190">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="7a583-191">Ad esempio, questa query LINQ:</span><span class="sxs-lookup"><span data-stu-id="7a583-191">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="7a583-192">Was on SQL Server formalmente convertita in:</span><span class="sxs-lookup"><span data-stu-id="7a583-192">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="7a583-193">Ma ora viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="7a583-193">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="7a583-194">Preview 5</span><span class="sxs-lookup"><span data-stu-id="7a583-194">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="7a583-195">Regole di confronto del database</span><span class="sxs-lookup"><span data-stu-id="7a583-195">Database collations</span></span>

<span data-ttu-id="7a583-196">È ora possibile specificare le regole di confronto predefinite per un database nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="7a583-196">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="7a583-197">Questa operazione passerà a migrazioni generate per impostare le regole di confronto durante la creazione del database.</span><span class="sxs-lookup"><span data-stu-id="7a583-197">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="7a583-198">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-198">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7a583-199">Migrations genera quindi quanto segue per creare il database in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a583-199">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="7a583-200">È inoltre possibile specificare le regole di confronto da utilizzare per colonne di database specifiche.</span><span class="sxs-lookup"><span data-stu-id="7a583-200">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="7a583-201">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-201">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7a583-202">Per coloro che non utilizzano migrazioni, le regole di confronto sono ora decodificate dal database durante l'impalcatura di un DbContext.</span><span class="sxs-lookup"><span data-stu-id="7a583-202">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="7a583-203">Infine, `EF.Functions.Collate()` consente di eseguire query ad hoc con regole di confronto diverse.</span><span class="sxs-lookup"><span data-stu-id="7a583-203">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="7a583-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-204">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="7a583-205">Verrà generata la query seguente per SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a583-205">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="7a583-206">Si noti che le regole di confronto ad hoc devono essere utilizzate con cautela, in quanto possono influire negativamente sulle prestazioni del database.</span><span class="sxs-lookup"><span data-stu-id="7a583-206">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="7a583-207">La documentazione viene rilevata in base al problema [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="7a583-207">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="7a583-208">Argomenti del flusso in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="7a583-208">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="7a583-209">Gli argomenti vengono ora propagati dalla riga di comando nel `CreateDbContext` metodo di [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="7a583-209">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="7a583-210">Per indicare, ad esempio, che si tratta di una build di sviluppo, un argomento personalizzato, ad esempio, `dev` può essere passato dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="7a583-210">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="7a583-211">Questo argomento viene quindi inserito nella Factory, dove può essere usato per controllare la modalità di creazione e inizializzazione del contesto.</span><span class="sxs-lookup"><span data-stu-id="7a583-211">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="7a583-212">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-212">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="7a583-213">La documentazione viene rilevata in base al problema [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="7a583-213">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="7a583-214">Query senza rilevamento con risoluzione di identità</span><span class="sxs-lookup"><span data-stu-id="7a583-214">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="7a583-215">È ora possibile configurare le query senza rilevamento per eseguire la risoluzione dell'identità.</span><span class="sxs-lookup"><span data-stu-id="7a583-215">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="7a583-216">La query seguente, ad esempio, creerà una nuova istanza di Blog per ogni post, anche se ogni Blog ha la stessa chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="7a583-216">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7a583-217">Tuttavia, a scapito di solito è leggermente più lento e si utilizza sempre una maggiore quantità di memoria, questa query può essere modificata per garantire la creazione di una sola istanza di Blog:</span><span class="sxs-lookup"><span data-stu-id="7a583-217">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7a583-218">Si noti che questa operazione è utile solo per le query senza rilevamento perché tutte le query di rilevamento presentano già questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="7a583-218">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="7a583-219">Inoltre, dopo la revisione dell'API, la `PerformIdentityResolution` sintassi verrà modificata.</span><span class="sxs-lookup"><span data-stu-id="7a583-219">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="7a583-220">Vedere [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="7a583-220">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="7a583-221">La documentazione viene rilevata in base al problema [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="7a583-221">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="7a583-222">Colonne calcolate (rese permanente) archiviate</span><span class="sxs-lookup"><span data-stu-id="7a583-222">Stored (persisted) computed columns</span></span>

<span data-ttu-id="7a583-223">La maggior parte dei database consente di archiviare i valori delle colonne calcolate dopo il calcolo.</span><span class="sxs-lookup"><span data-stu-id="7a583-223">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="7a583-224">Quando questo occupa spazio su disco, la colonna calcolata viene calcolata una sola volta in Update, anziché ogni volta che viene recuperato il valore.</span><span class="sxs-lookup"><span data-stu-id="7a583-224">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="7a583-225">Consente inoltre di indicizzare la colonna per alcuni database.</span><span class="sxs-lookup"><span data-stu-id="7a583-225">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="7a583-226">EF Core 5,0 consente la configurazione delle colonne calcolate come archiviate.</span><span class="sxs-lookup"><span data-stu-id="7a583-226">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="7a583-227">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-227">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="7a583-228">Colonne calcolate SQLite</span><span class="sxs-lookup"><span data-stu-id="7a583-228">SQLite computed columns</span></span>

<span data-ttu-id="7a583-229">EF Core supporta ora le colonne calcolate nei database SQLite.</span><span class="sxs-lookup"><span data-stu-id="7a583-229">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="7a583-230">Preview 4</span><span class="sxs-lookup"><span data-stu-id="7a583-230">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="7a583-231">Configurare la precisione del database/ridimensionare il modello</span><span class="sxs-lookup"><span data-stu-id="7a583-231">Configure database precision/scale in model</span></span>

<span data-ttu-id="7a583-232">È ora possibile specificare la precisione e la scala di una proprietà usando il generatore di modelli.</span><span class="sxs-lookup"><span data-stu-id="7a583-232">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="7a583-233">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-233">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="7a583-234">La precisione e la scala possono comunque essere impostate tramite il tipo di database completo, ad esempio "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="7a583-234">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="7a583-235">La documentazione viene rilevata in base al problema [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="7a583-235">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="7a583-236">Specificare il fattore di riempimento dell'indice SQL Server</span><span class="sxs-lookup"><span data-stu-id="7a583-236">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="7a583-237">È ora possibile specificare il fattore di riempimento quando si crea un indice in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7a583-237">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="7a583-238">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-238">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="7a583-239">Preview 3</span><span class="sxs-lookup"><span data-stu-id="7a583-239">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="7a583-240">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="7a583-240">Filtered Include</span></span>

<span data-ttu-id="7a583-241">Il metodo include ora supporta il filtraggio delle entità incluse.</span><span class="sxs-lookup"><span data-stu-id="7a583-241">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="7a583-242">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-242">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="7a583-243">Questa query restituirà i Blog insieme a ogni post associato, ma solo quando il titolo post contiene "Cheese".</span><span class="sxs-lookup"><span data-stu-id="7a583-243">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="7a583-244">È possibile utilizzare Skip e Take anche per ridurre il numero di entità incluse.</span><span class="sxs-lookup"><span data-stu-id="7a583-244">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="7a583-245">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-245">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="7a583-246">Questa query restituirà Blog con al massimo cinque post inclusi in ogni blog.</span><span class="sxs-lookup"><span data-stu-id="7a583-246">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="7a583-247">Per informazioni dettagliate, vedere la [documentazione di inclusione](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="7a583-247">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="7a583-248">Nuova API ModelBuilder per le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="7a583-248">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="7a583-249">Le proprietà di navigazione vengono configurate principalmente durante la [definizione delle relazioni](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7a583-249">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="7a583-250">Tuttavia, il nuovo `Navigation` metodo può essere utilizzato nei casi in cui le proprietà di navigazione richiedono una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="7a583-250">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="7a583-251">Ad esempio, per impostare un campo di supporto per la navigazione quando il campo non viene trovato per convenzione:</span><span class="sxs-lookup"><span data-stu-id="7a583-251">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="7a583-252">Si noti che l' `Navigation` API non sostituisce la configurazione della relazione.</span><span class="sxs-lookup"><span data-stu-id="7a583-252">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="7a583-253">Consente invece una configurazione aggiuntiva delle proprietà di navigazione nelle relazioni già individuate o definite.</span><span class="sxs-lookup"><span data-stu-id="7a583-253">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="7a583-254">Vedere la [documentazione relativa alla configurazione delle proprietà di navigazione](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="7a583-254">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="7a583-255">Nuovi parametri della riga di comando per spazi dei nomi e stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="7a583-255">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="7a583-256">Le migrazioni e le impalcature consentono ora di specificare gli spazi dei nomi nella riga di comando.</span><span class="sxs-lookup"><span data-stu-id="7a583-256">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="7a583-257">Ad esempio, per decompilare un database inserendo le classi context e Model in spazi dei nomi diversi:</span><span class="sxs-lookup"><span data-stu-id="7a583-257">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="7a583-258">Per informazioni dettagliate, vedere la documentazione sulle [migrazioni](xref:core/managing-schemas/migrations/index#namespaces) e la [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="7a583-258">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="7a583-259">Inoltre, è ora possibile passare una stringa di connessione al `database-update` comando:</span><span class="sxs-lookup"><span data-stu-id="7a583-259">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="7a583-260">Per informazioni dettagliate, vedere la [documentazione degli strumenti](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="7a583-260">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="7a583-261">Sono stati anche aggiunti parametri equivalenti ai comandi di PowerShell usati nella console di gestione pacchetti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7a583-261">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="7a583-262">EnableDetailedErrors ha restituito</span><span class="sxs-lookup"><span data-stu-id="7a583-262">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="7a583-263">Per motivi di prestazioni, EF non esegue controlli null aggiuntivi durante la lettura dei valori dal database.</span><span class="sxs-lookup"><span data-stu-id="7a583-263">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="7a583-264">Ciò può causare eccezioni difficili da radice quando viene rilevato un valore null imprevisto.</span><span class="sxs-lookup"><span data-stu-id="7a583-264">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="7a583-265">`EnableDetailedErrors`Se si utilizza, verrà aggiunto un controllo null aggiuntivo alle query in modo che, per un lieve sovraccarico delle prestazioni, questi errori siano più semplici da risalire a una causa radice.</span><span class="sxs-lookup"><span data-stu-id="7a583-265">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="7a583-266">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-266">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="7a583-267">La documentazione viene rilevata in base al problema [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="7a583-267">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="7a583-268">Chiavi di partizione Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a583-268">Cosmos partition keys</span></span>

<span data-ttu-id="7a583-269">La chiave di partizione da utilizzare per una determinata query può ora essere specificata nella query.</span><span class="sxs-lookup"><span data-stu-id="7a583-269">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="7a583-270">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-270">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="7a583-271">La documentazione viene rilevata in base al problema [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="7a583-271">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="7a583-272">Supporto per la funzione SQL Server DataLength</span><span class="sxs-lookup"><span data-stu-id="7a583-272">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="7a583-273">È possibile accedervi usando il nuovo `EF.Functions.DataLength` metodo.</span><span class="sxs-lookup"><span data-stu-id="7a583-273">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="7a583-274">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-274">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="7a583-275">Preview 2</span><span class="sxs-lookup"><span data-stu-id="7a583-275">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="7a583-276">Usare un attributo C# per specificare un campo di supporto delle proprietà</span><span class="sxs-lookup"><span data-stu-id="7a583-276">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="7a583-277">È ora possibile usare un attributo C# per specificare il campo sottostante per una proprietà.</span><span class="sxs-lookup"><span data-stu-id="7a583-277">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="7a583-278">Questo attributo consente a EF Core di continuare a scrivere e leggere dal campo sottostante come normalmente accade, anche quando il campo sottostante non viene trovato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="7a583-278">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="7a583-279">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-279">For example:</span></span>

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

<span data-ttu-id="7a583-280">La documentazione viene rilevata in base al problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="7a583-280">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="7a583-281">Mapping completo del discriminatore</span><span class="sxs-lookup"><span data-stu-id="7a583-281">Complete discriminator mapping</span></span>

<span data-ttu-id="7a583-282">EF Core utilizza una colonna discriminatore per il [mapping TPH di una gerarchia di ereditarietà](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="7a583-282">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="7a583-283">Alcune ottimizzazioni delle prestazioni sono possibili purché EF Core conosca tutti i valori possibili per il discriminatore.</span><span class="sxs-lookup"><span data-stu-id="7a583-283">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="7a583-284">EF Core 5,0 implementa ora questi miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="7a583-284">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="7a583-285">Ad esempio, le versioni precedenti di EF Core generano sempre questo SQL per una query che restituisce tutti i tipi in una gerarchia:</span><span class="sxs-lookup"><span data-stu-id="7a583-285">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="7a583-286">EF Core 5,0 genera ora quanto segue quando viene configurato un mapping del discriminatore completo:</span><span class="sxs-lookup"><span data-stu-id="7a583-286">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="7a583-287">Si tratta del comportamento predefinito che inizia con Preview 3.</span><span class="sxs-lookup"><span data-stu-id="7a583-287">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="7a583-288">Miglioramenti delle prestazioni in Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="7a583-288">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="7a583-289">Sono stati apportati due miglioramenti alle prestazioni per SQLIte:</span><span class="sxs-lookup"><span data-stu-id="7a583-289">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="7a583-290">Il recupero di dati binari e di tipo stringa con GetBytes, GetChars e GetTextReader è ora più efficiente mediante l'uso di SqliteBlob e flussi.</span><span class="sxs-lookup"><span data-stu-id="7a583-290">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="7a583-291">L'inizializzazione di SqliteConnection è ora Lazy.</span><span class="sxs-lookup"><span data-stu-id="7a583-291">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="7a583-292">Questi miglioramenti si trovano nel provider Microsoft. Data. sqlite ADO.NET e quindi migliorano le prestazioni al di fuori della EF Core.</span><span class="sxs-lookup"><span data-stu-id="7a583-292">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="7a583-293">Preview 1</span><span class="sxs-lookup"><span data-stu-id="7a583-293">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="7a583-294">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="7a583-294">Simple logging</span></span>

<span data-ttu-id="7a583-295">Questa funzionalità aggiunge funzionalità simili a `Database.Log` EF6.</span><span class="sxs-lookup"><span data-stu-id="7a583-295">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="7a583-296">Ovvero, fornisce un modo semplice per ottenere i log da EF Core senza la necessità di configurare alcun tipo di Framework di registrazione esterno.</span><span class="sxs-lookup"><span data-stu-id="7a583-296">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="7a583-297">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7a583-297">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7a583-298">La documentazione aggiuntiva viene rilevata in base al problema [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="7a583-298">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="7a583-299">Modo semplice per ottenere SQL generato</span><span class="sxs-lookup"><span data-stu-id="7a583-299">Simple way to get generated SQL</span></span>

<span data-ttu-id="7a583-300">EF Core 5,0 introduce il `ToQueryString` metodo di estensione, che restituirà il SQL che EF Core genererà durante l'esecuzione di una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="7a583-300">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="7a583-301">La documentazione preliminare è inclusa nello [stato settimanale EF per il 9 gennaio 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="7a583-301">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="7a583-302">La documentazione aggiuntiva viene rilevata in base al problema [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="7a583-302">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="7a583-303">Usare un attributo C# per indicare che un'entità non ha una chiave</span><span class="sxs-lookup"><span data-stu-id="7a583-303">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="7a583-304">È ora possibile configurare un tipo di entità senza chiavi usando il nuovo `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7a583-304">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="7a583-305">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-305">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="7a583-306">La documentazione viene rilevata in base al problema [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="7a583-306">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="7a583-307">La stringa di connessione o di connessione può essere modificata in DbContext inizializzato</span><span class="sxs-lookup"><span data-stu-id="7a583-307">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="7a583-308">È ora più semplice creare un'istanza di DbContext senza connessione o stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="7a583-308">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="7a583-309">Inoltre, la connessione o la stringa di connessione possono ora essere mutate sull'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="7a583-309">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="7a583-310">Questa funzionalità consente alla stessa istanza del contesto di connettersi dinamicamente a database diversi.</span><span class="sxs-lookup"><span data-stu-id="7a583-310">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="7a583-311">La documentazione viene rilevata in base al problema [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="7a583-311">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="7a583-312">Proxy di rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="7a583-312">Change-tracking proxies</span></span>

<span data-ttu-id="7a583-313">EF Core ora possono generare proxy di runtime che implementano automaticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) e [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="7a583-313">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="7a583-314">Queste segnalano quindi le modifiche ai valori delle proprietà dell'entità direttamente a EF Core, evitando la necessità di analizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="7a583-314">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="7a583-315">Tuttavia, i proxy presentano un set di limitazioni, quindi non sono destinati a tutti.</span><span class="sxs-lookup"><span data-stu-id="7a583-315">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="7a583-316">La documentazione viene rilevata in base al problema [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="7a583-316">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="7a583-317">Viste di debug migliorate</span><span class="sxs-lookup"><span data-stu-id="7a583-317">Enhanced debug views</span></span>

<span data-ttu-id="7a583-318">Le visualizzazioni di debug rappresentano un modo semplice per esaminare gli elementi interni di EF Core durante il debug dei problemi.</span><span class="sxs-lookup"><span data-stu-id="7a583-318">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="7a583-319">Una visualizzazione di debug per il modello è stata implementata qualche tempo fa.</span><span class="sxs-lookup"><span data-stu-id="7a583-319">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="7a583-320">Per EF Core 5,0, la visualizzazione modello è stata semplificata per la lettura e l'aggiunta di una nuova visualizzazione debug per le entità rilevate nel gestore di stato.</span><span class="sxs-lookup"><span data-stu-id="7a583-320">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="7a583-321">La documentazione preliminare è inclusa nello [stato settimanale EF per il 12 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="7a583-321">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="7a583-322">La documentazione aggiuntiva viene rilevata in base al problema [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="7a583-322">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="7a583-323">Gestione migliorata della semantica null del database</span><span class="sxs-lookup"><span data-stu-id="7a583-323">Improved handling of database null semantics</span></span>

<span data-ttu-id="7a583-324">I database relazionali considerano in genere NULL come valore sconosciuto e pertanto non uguale a qualsiasi altro valore NULL.</span><span class="sxs-lookup"><span data-stu-id="7a583-324">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="7a583-325">Mentre C# considera null come un valore definito, che confronta uguale a qualsiasi altro valore null.</span><span class="sxs-lookup"><span data-stu-id="7a583-325">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="7a583-326">EF Core per impostazione predefinita converte le query in modo che utilizzino la semantica null di C#.</span><span class="sxs-lookup"><span data-stu-id="7a583-326">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="7a583-327">EF Core 5,0 migliora notevolmente l'efficienza di queste traduzioni.</span><span class="sxs-lookup"><span data-stu-id="7a583-327">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="7a583-328">La documentazione viene rilevata in base al problema [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="7a583-328">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="7a583-329">Proprietà indicizzatore</span><span class="sxs-lookup"><span data-stu-id="7a583-329">Indexer properties</span></span>

<span data-ttu-id="7a583-330">EF Core 5,0 supporta il mapping delle proprietà dell'indicizzatore C#.</span><span class="sxs-lookup"><span data-stu-id="7a583-330">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="7a583-331">Queste proprietà consentono alle entità di fungere da contenitori di proprietà in cui viene eseguito il mapping delle colonne alle proprietà denominate nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="7a583-331">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="7a583-332">La documentazione viene rilevata in base al problema [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="7a583-332">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="7a583-333">Generazione di vincoli check per i mapping enum</span><span class="sxs-lookup"><span data-stu-id="7a583-333">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="7a583-334">EF Core 5,0 le migrazioni possono ora generare vincoli CHECK per i mapping delle proprietà di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="7a583-334">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="7a583-335">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-335">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="7a583-336">La documentazione viene rilevata in base al problema [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="7a583-336">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="7a583-337">Relazionale</span><span class="sxs-lookup"><span data-stu-id="7a583-337">IsRelational</span></span>

<span data-ttu-id="7a583-338">`IsRelational`È stato aggiunto un nuovo metodo, oltre a quello esistente `IsSqlServer` , `IsSqlite` , e `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="7a583-338">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="7a583-339">Questo metodo può essere utilizzato per verificare se DbContext utilizza un provider di database relazionale.</span><span class="sxs-lookup"><span data-stu-id="7a583-339">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="7a583-340">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-340">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="7a583-341">La documentazione viene rilevata in base al problema [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="7a583-341">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="7a583-342">Concorrenza ottimistica di Cosmos con ETag</span><span class="sxs-lookup"><span data-stu-id="7a583-342">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="7a583-343">Il provider di database Azure Cosmos DB supporta ora la concorrenza ottimistica tramite ETag.</span><span class="sxs-lookup"><span data-stu-id="7a583-343">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="7a583-344">Usare il generatore di modelli in OnModelCreating per configurare un ETag:</span><span class="sxs-lookup"><span data-stu-id="7a583-344">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="7a583-345">SaveChanges genera quindi un'operazione `DbUpdateConcurrencyException` in un conflitto di concorrenza, che [può essere gestita](https://docs.microsoft.com/ef/core/saving/concurrency) per implementare nuovi tentativi e così via.</span><span class="sxs-lookup"><span data-stu-id="7a583-345">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="7a583-346">La documentazione viene rilevata in base al problema [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="7a583-346">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="7a583-347">Eseguire query sulle traduzioni per più costrutti DateTime</span><span class="sxs-lookup"><span data-stu-id="7a583-347">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="7a583-348">Vengono ora convertite le query contenenti nuove costruzioni DateTime.</span><span class="sxs-lookup"><span data-stu-id="7a583-348">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="7a583-349">Inoltre, vengono ora mappate le funzioni di SQL Server seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a583-349">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="7a583-350">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="7a583-350">DateDiffWeek</span></span>
* <span data-ttu-id="7a583-351">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="7a583-351">DateFromParts</span></span>

<span data-ttu-id="7a583-352">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-352">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="7a583-353">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="7a583-353">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="7a583-354">Eseguire query sulle traduzioni per altri costrutti di matrici di byte</span><span class="sxs-lookup"><span data-stu-id="7a583-354">Query translations for more byte array constructs</span></span>

<span data-ttu-id="7a583-355">Le query che usano le proprietà Contains, length, SequenceEqual e così via su byte [] vengono ora convertite in SQL.</span><span class="sxs-lookup"><span data-stu-id="7a583-355">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="7a583-356">La documentazione preliminare è inclusa nello [stato settimanale EF del 5 dicembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7a583-356">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7a583-357">La documentazione aggiuntiva viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="7a583-357">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="7a583-358">Conversione di query per invertire</span><span class="sxs-lookup"><span data-stu-id="7a583-358">Query translation for Reverse</span></span>

<span data-ttu-id="7a583-359">Le query che utilizzano `Reverse` ora vengono convertite.</span><span class="sxs-lookup"><span data-stu-id="7a583-359">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="7a583-360">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-360">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="7a583-361">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="7a583-361">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="7a583-362">Conversione di query per operatori bit per bit</span><span class="sxs-lookup"><span data-stu-id="7a583-362">Query translation for bitwise operators</span></span>

<span data-ttu-id="7a583-363">Le query che utilizzano operatori bit per bit vengono ora convertite in più casi, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a583-363">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="7a583-364">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="7a583-364">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="7a583-365">Conversione di query per le stringhe in Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a583-365">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="7a583-366">Le query che usano i metodi String contengono, StartsWith e EndsWith vengono convertite quando si usa il provider di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7a583-366">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="7a583-367">La documentazione viene rilevata in base al problema [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="7a583-367">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
