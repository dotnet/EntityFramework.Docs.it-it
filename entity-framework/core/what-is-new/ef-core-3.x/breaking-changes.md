---
title: Modifiche di rilievo in EF Core 3. x-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: bfcfb7257091d1b6889f7c0af00ddab10e0e12e3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429312"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="9e494-103">Modifiche di rilievo incluse in EF Core 3. x</span><span class="sxs-lookup"><span data-stu-id="9e494-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="9e494-104">Le modifiche alle API e al comportamento seguenti possono causare l'interruzione delle applicazioni esistenti durante l'aggiornamento a 3. x.</span><span class="sxs-lookup"><span data-stu-id="9e494-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="9e494-105">Le modifiche che si prevede abbiano impatto solo sui provider di database sono documentate nelle [modifiche che influiscono sul provider](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="9e494-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="9e494-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9e494-106">Summary</span></span>

| <span data-ttu-id="9e494-107">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="9e494-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="9e494-108">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="9e494-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="9e494-109">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="9e494-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="9e494-110">Alto</span><span class="sxs-lookup"><span data-stu-id="9e494-110">High</span></span>       |
| [<span data-ttu-id="9e494-111">Lo strumento da riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="9e494-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="9e494-112">Alto</span><span class="sxs-lookup"><span data-stu-id="9e494-112">High</span></span>      |
| [<span data-ttu-id="9e494-113">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="9e494-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="9e494-114">Alto</span><span class="sxs-lookup"><span data-stu-id="9e494-114">High</span></span>      |
| [<span data-ttu-id="9e494-115">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="9e494-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="9e494-116">Alto</span><span class="sxs-lookup"><span data-stu-id="9e494-116">High</span></span>      |
| [<span data-ttu-id="9e494-117">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="9e494-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="9e494-118">Alto</span><span class="sxs-lookup"><span data-stu-id="9e494-118">High</span></span>      |
| [<span data-ttu-id="9e494-119">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e494-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="9e494-120">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-120">Medium</span></span>      |
| [<span data-ttu-id="9e494-121">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="9e494-122">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-122">Medium</span></span>      |
| [<span data-ttu-id="9e494-123">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="9e494-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="9e494-124">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-124">Medium</span></span>      |
| [<span data-ttu-id="9e494-125">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="9e494-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="9e494-126">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-126">Medium</span></span>      |
| [<span data-ttu-id="9e494-127">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="9e494-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="9e494-128">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-128">Medium</span></span>      |
| [<span data-ttu-id="9e494-129">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="9e494-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="9e494-130">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-130">Medium</span></span>      |
| [<span data-ttu-id="9e494-131">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="9e494-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="9e494-132">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-132">Medium</span></span>      |
| [<span data-ttu-id="9e494-133">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="9e494-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="9e494-134">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-134">Medium</span></span>      |
| [<span data-ttu-id="9e494-135">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="9e494-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="9e494-136">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-136">Medium</span></span>      |
| [<span data-ttu-id="9e494-137">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="9e494-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="9e494-138">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-138">Medium</span></span>      |
| [<span data-ttu-id="9e494-139">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="9e494-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="9e494-140">Media</span><span class="sxs-lookup"><span data-stu-id="9e494-140">Medium</span></span>      |
| [<span data-ttu-id="9e494-141">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="9e494-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="9e494-142">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-142">Low</span></span>      |
| [<span data-ttu-id="9e494-143">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="9e494-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="9e494-144">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-144">Low</span></span>      |
| [<span data-ttu-id="9e494-145">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="9e494-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="9e494-146">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-146">Low</span></span>      |
| [<span data-ttu-id="9e494-147">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="9e494-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="9e494-148">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-148">Low</span></span>      |
| [<span data-ttu-id="9e494-149">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="9e494-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="9e494-150">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-150">Low</span></span>      |
| [<span data-ttu-id="9e494-151">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="9e494-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="9e494-152">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-152">Low</span></span>      |
| [<span data-ttu-id="9e494-153">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="9e494-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="9e494-154">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-154">Low</span></span>      |
| [<span data-ttu-id="9e494-155">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="9e494-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="9e494-156">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-156">Low</span></span>      |
| [<span data-ttu-id="9e494-157">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="9e494-158">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-158">Low</span></span>      |
| [<span data-ttu-id="9e494-159">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="9e494-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="9e494-160">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-160">Low</span></span>      |
| [<span data-ttu-id="9e494-161">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="9e494-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="9e494-162">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-162">Low</span></span>      |
| [<span data-ttu-id="9e494-163">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="9e494-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="9e494-164">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-164">Low</span></span>      |
| [<span data-ttu-id="9e494-165">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="9e494-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="9e494-166">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-166">Low</span></span>      |
| [<span data-ttu-id="9e494-167">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="9e494-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="9e494-168">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-168">Low</span></span>      |
| [<span data-ttu-id="9e494-169">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="9e494-170">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-170">Low</span></span>      |
| [<span data-ttu-id="9e494-171">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="9e494-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="9e494-172">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-172">Low</span></span>      |
| [<span data-ttu-id="9e494-173">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="9e494-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="9e494-174">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-174">Low</span></span>      |
| [<span data-ttu-id="9e494-175">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="9e494-176">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-176">Low</span></span>      |
| [<span data-ttu-id="9e494-177">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="9e494-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="9e494-178">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-178">Low</span></span>      |
| [<span data-ttu-id="9e494-179">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="9e494-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="9e494-180">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-180">Low</span></span>      |
| [<span data-ttu-id="9e494-181">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="9e494-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="9e494-182">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-182">Low</span></span>      |
| [<span data-ttu-id="9e494-183">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="9e494-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="9e494-184">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-184">Low</span></span>      |
| [<span data-ttu-id="9e494-185">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="9e494-186">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-186">Low</span></span>      |
| [<span data-ttu-id="9e494-187">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="9e494-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="9e494-188">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-188">Low</span></span>      |
| [<span data-ttu-id="9e494-189">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="9e494-190">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-190">Low</span></span>      |
| [<span data-ttu-id="9e494-191">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="9e494-192">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-192">Low</span></span>      |
| [<span data-ttu-id="9e494-193">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="9e494-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="9e494-194">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-194">Low</span></span>      |
| [<span data-ttu-id="9e494-195">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="9e494-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="9e494-196">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-196">Low</span></span>      |
| [<span data-ttu-id="9e494-197">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="9e494-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="9e494-198">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-198">Low</span></span>      |
| [<span data-ttu-id="9e494-199">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="9e494-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="9e494-200">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-200">Low</span></span>      |
| [<span data-ttu-id="9e494-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="9e494-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="9e494-202">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-202">Low</span></span>      |
| [<span data-ttu-id="9e494-203">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="9e494-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="9e494-204">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-204">Low</span></span>      |
| [<span data-ttu-id="9e494-205">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="9e494-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="9e494-206">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-206">Low</span></span>      |
| [<span data-ttu-id="9e494-207">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="9e494-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="9e494-208">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-208">Low</span></span>      |
| [<span data-ttu-id="9e494-209">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="9e494-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="9e494-210">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-210">Low</span></span>      |
| [<span data-ttu-id="9e494-211">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="9e494-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="9e494-212">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-212">Low</span></span>      |
| [<span data-ttu-id="9e494-213">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="9e494-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="9e494-214">Basso</span><span class="sxs-lookup"><span data-stu-id="9e494-214">Low</span></span>      |
| [<span data-ttu-id="9e494-215">~~EF Core 3,0 destinazioni .NET Standard 2,1 anziché .NET Standard 2,0~~ Ripristinato</span><span class="sxs-lookup"><span data-stu-id="9e494-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="9e494-216">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="9e494-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="9e494-217">Modifiche a elevato effetto</span><span class="sxs-lookup"><span data-stu-id="9e494-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="9e494-218">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="9e494-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="9e494-219">[Rilevamento del problema #14935](https://github.com/dotnet/efcore/issues/14935) 
 [Vedere anche problema #12795](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="9e494-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="9e494-220">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-220">Old behavior</span></span>

<span data-ttu-id="9e494-221">Nelle versioni precedenti alla versione 3.0, quando EF Core non era in grado di convertire un'espressione inclusa in una query in SQL o in un parametro, l'espressione veniva automaticamente valutata nel client.</span><span class="sxs-lookup"><span data-stu-id="9e494-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="9e494-222">Per impostazione predefinita, la valutazione client di espressioni potenzialmente dispendiose si limitava ad attivare solo un avviso.</span><span class="sxs-lookup"><span data-stu-id="9e494-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-223">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-223">New behavior</span></span>

<span data-ttu-id="9e494-224">A partire dalla versione 3.0, EF Core consente solo la valutazione delle espressioni nella proiezione di primo livello (l'ultima chiamata `Select()` nella query) nel client.</span><span class="sxs-lookup"><span data-stu-id="9e494-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="9e494-225">Quando le espressioni in altre posizioni all'interno della query non possono essere convertite in SQL o in un parametro, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9e494-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-226">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-226">Why</span></span>

<span data-ttu-id="9e494-227">La valutazione client automatica delle query consente di eseguire numerose query anche nel caso in cui parti importanti delle query non possono essere convertite.</span><span class="sxs-lookup"><span data-stu-id="9e494-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="9e494-228">Questo comportamento può causare un comportamento imprevisto e potenzialmente dannoso che può diventare evidente solo in produzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="9e494-229">Ad esempio, una condizione in una chiamata `Where()` che non può essere convertita può causare il trasferimento di tutte le righe della tabella del server di database e l'applicazione del filtro nel client.</span><span class="sxs-lookup"><span data-stu-id="9e494-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="9e494-230">È probabile che questa situazione non venga rilevata se la tabella contiene solo alcune righe in fase di sviluppo, ma che abbia un grande impatto quando l'applicazione passa in produzione dove la tabella può contenere milioni di righe.</span><span class="sxs-lookup"><span data-stu-id="9e494-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="9e494-231">Gli avvisi di valutazione client inoltre si sono rivelati molto facili da ignorare durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9e494-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="9e494-232">Inoltre, la valutazione client automatica può causare problemi in cui il miglioramento della conversione di query per espressioni specifiche causa modifiche impreviste che causano un'interruzione da una versione all'altra.</span><span class="sxs-lookup"><span data-stu-id="9e494-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-233">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-233">Mitigations</span></span>

<span data-ttu-id="9e494-234">Se una query non può essere convertita completamente, riscrivere la query in un formato che possa essere convertito o usare `AsEnumerable()`, `ToList()` o un elemento simile per riportare in modo esplicito i dati nel client dove possono essere quindi ulteriormente elaborati usando LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="9e494-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="9e494-235">Modifiche a media Impact</span><span class="sxs-lookup"><span data-stu-id="9e494-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="9e494-236">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e494-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="9e494-237">Annunci problema n. 325</span><span class="sxs-lookup"><span data-stu-id="9e494-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-238">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-238">Old behavior</span></span>

<span data-ttu-id="9e494-239">Nelle versioni precedenti ad ASP.NET Core 3.0, quando si aggiungeva un riferimento a un pacchetto in `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, veniva inserito EF Core e alcuni dei provider di dati di EF Core come il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9e494-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-240">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-240">New behavior</span></span>

<span data-ttu-id="9e494-241">A partire dalla versione 3.0, il framework condiviso di ASP.NET Core non include EF Core o provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-242">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-242">Why</span></span>

<span data-ttu-id="9e494-243">Prima di questa modifica, per ottenere EF Core erano necessarie procedure diverse, a seconda che l'applicazione avesse o meno come destinazione ASP.NET Core e SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9e494-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="9e494-244">Inoltre, l'aggiornamento di ASP.NET Core forzava l'aggiornamento di EF Core e del provider di SQL Server, non sempre auspicabile.</span><span class="sxs-lookup"><span data-stu-id="9e494-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="9e494-245">Con questa modifica, la procedura per ottenere EF Core è la stessa in tutti i provider, le implementazioni .NET supportate e i tipi di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="9e494-246">Gli sviluppatori possono ora controllare anche in modo preciso quando vengono aggiornati EF Core e i provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-247">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-247">Mitigations</span></span>

<span data-ttu-id="9e494-248">Per usare EF Core in un'applicazione ASP.NET Core 3.0 o in un'altra applicazione supportata, aggiungere in modo esplicito un riferimento al pacchetto al provider di database di EF Core che verrà usato dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="9e494-249">Lo strumento della riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="9e494-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="9e494-250">Problema n. 14016</span><span class="sxs-lookup"><span data-stu-id="9e494-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-251">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-251">Old behavior</span></span>

<span data-ttu-id="9e494-252">Prima della versione 3.0, lo strumento `dotnet ef` era incluso in .NET Core SDK ed era immediatamente disponibile dalla riga di comando di qualsiasi progetto senza richiedere passaggi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="9e494-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-253">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-253">New behavior</span></span>

<span data-ttu-id="9e494-254">A partire dalla versione 3.0, .NET SDK non include lo strumento `dotnet ef` pertanto, prima di poterlo usare, è necessario installarlo in modo esplicito come strumento locale o globale.</span><span class="sxs-lookup"><span data-stu-id="9e494-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-255">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-255">Why</span></span>

<span data-ttu-id="9e494-256">Questa modifica consente di distribuire e aggiornare `dotnet ef` come uno strumento della riga di comando di .NET in NuGet, coerentemente con il fatto che anche EF Core 3.0 viene distribuito come pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="9e494-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-257">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-257">Mitigations</span></span>

<span data-ttu-id="9e494-258">Per essere in grado di gestire le migrazioni o eseguire lo scaffolding di `DbContext`, installare `dotnet-ef` come strumento globale:</span><span class="sxs-lookup"><span data-stu-id="9e494-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="9e494-259">È inoltre possibile ottenerlo come strumento locale quando si ripristinano le dipendenze di un progetto che lo dichiara come dipendenza di strumenti utilizzando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="9e494-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="9e494-260">Modifiche a basso effetto</span><span class="sxs-lookup"><span data-stu-id="9e494-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="9e494-261">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="9e494-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="9e494-262">Problema n. 10996</span><span class="sxs-lookup"><span data-stu-id="9e494-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-263">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-263">Old behavior</span></span>

<span data-ttu-id="9e494-264">Prima di EF Core 3.0, erano disponibili overload per questi nomi di metodo per supportare l'uso con una stringa normale o una stringa che deve essere interpolata in SQL e parametri.</span><span class="sxs-lookup"><span data-stu-id="9e494-264">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-265">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-265">New behavior</span></span>

<span data-ttu-id="9e494-266">A partire da EF Core 3.0, usare `FromSqlRaw`, `ExecuteSqlRaw` e `ExecuteSqlRawAsync` per creare una query con parametri in cui i parametri vengono passati separatamente dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="9e494-266">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="9e494-267">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-267">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="9e494-268">Usare `FromSqlInterpolated`, `ExecuteSqlInterpolated`, e `ExecuteSqlInterpolatedAsync` per creare una query con parametri in cui i parametri vengono passati come parte di una stringa di query interpolata.</span><span class="sxs-lookup"><span data-stu-id="9e494-268">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="9e494-269">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-269">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="9e494-270">Si noti che entrambe le query precedenti produrranno lo stesso codice SQL con parametri con gli stessi parametri SQL.</span><span class="sxs-lookup"><span data-stu-id="9e494-270">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-271">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-271">Why</span></span>

<span data-ttu-id="9e494-272">Con gli overload di metodi come questi, è molto facile chiamare accidentalmente il metodo con stringa non elaborata anche se l'intento era chiamare il metodo con stringa interpolata e viceversa.</span><span class="sxs-lookup"><span data-stu-id="9e494-272">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="9e494-273">Il risultato potrebbero essere query senza parametri, quando invece è prevista la parametrizzazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-273">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-274">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-274">Mitigations</span></span>

<span data-ttu-id="9e494-275">Passare all'uso dei nuovi nomi di metodo.</span><span class="sxs-lookup"><span data-stu-id="9e494-275">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="9e494-276">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="9e494-276">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="9e494-277">Rilevamento del problema #15392</span><span class="sxs-lookup"><span data-stu-id="9e494-277">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-278">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-278">Old behavior</span></span>

<span data-ttu-id="9e494-279">Prima di EF Core 3,0, il metodo dati da tabelle ha tentato di rilevare se il SQL passato può essere composto in base a.</span><span class="sxs-lookup"><span data-stu-id="9e494-279">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="9e494-280">Ha fatto la valutazione del client quando SQL era non componibile come un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="9e494-280">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="9e494-281">La query seguente ha funzionato eseguendo il stored procedure sul server e FirstOrDefault sul lato client.</span><span class="sxs-lookup"><span data-stu-id="9e494-281">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="9e494-282">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-282">New behavior</span></span>

<span data-ttu-id="9e494-283">A partire da EF Core 3,0, EF Core non tenterà di analizzare SQL.</span><span class="sxs-lookup"><span data-stu-id="9e494-283">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="9e494-284">Quindi, se si esegue la composizione dopo FromSqlRaw/FromSqlInterpolated, EF Core comporrà il SQL causando una sottoquery.</span><span class="sxs-lookup"><span data-stu-id="9e494-284">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="9e494-285">Quindi, se si usa un stored procedure con Composition, si otterrà un'eccezione per la sintassi SQL non valida.</span><span class="sxs-lookup"><span data-stu-id="9e494-285">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-286">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-286">Why</span></span>

<span data-ttu-id="9e494-287">EF Core 3,0 non supporta la valutazione automatica del client perché è stata soggetta a errori, come illustrato [qui](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="9e494-287">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-288">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-288">Mitigations</span></span>

<span data-ttu-id="9e494-289">Se si usa un stored procedure in FromSqlRaw/FromSqlInterpolated, è noto che non è possibile componerlo, quindi è possibile aggiungere __AsEnumerable/AsAsyncEnumerable__ subito dopo la chiamata al metodo dati da tabelle per evitare qualsiasi composizione sul lato server.</span><span class="sxs-lookup"><span data-stu-id="9e494-289">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="9e494-290">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="9e494-290">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="9e494-291">Problema n. 15704</span><span class="sxs-lookup"><span data-stu-id="9e494-291">Tracking Issue #15704</span></span>](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-292">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-292">Old behavior</span></span>

<span data-ttu-id="9e494-293">Prima di EF Core 3.0, il metodo `FromSql` poteva essere specificato in un punto qualsiasi nella query.</span><span class="sxs-lookup"><span data-stu-id="9e494-293">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-294">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-294">New behavior</span></span>

<span data-ttu-id="9e494-295">A partire da EF Core 3.0, i nuovi metodi `FromSqlRaw` e `FromSqlInterpolated` (che sostituiscono`FromSql`) possono essere specificati solo per radici di query, ad esempio direttamente in `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="9e494-295">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="9e494-296">Qualsiasi tentativo di specificarli altrove causerà un errore di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-296">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-297">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-297">Why</span></span>

<span data-ttu-id="9e494-298">La specifica di `FromSql` in qualsiasi posizione diversa da un `DbSet` non ha alcun significato aggiuntivo oppure valore aggiunto e può causare ambiguità in determinati scenari.</span><span class="sxs-lookup"><span data-stu-id="9e494-298">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-299">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-299">Mitigations</span></span>

<span data-ttu-id="9e494-300">Le chiamate di `FromSql` devono essere spostate in modo da comparire direttamente nel `DbSet` a cui si applicano.</span><span class="sxs-lookup"><span data-stu-id="9e494-300">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="9e494-301">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="9e494-301">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="9e494-302">Problema n. 13518</span><span class="sxs-lookup"><span data-stu-id="9e494-302">Tracking Issue #13518</span></span>](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-303">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-303">Old behavior</span></span>

<span data-ttu-id="9e494-304">Prima di EF Core 3.0, la stessa istanza di un'entità poteva essere usata per ogni occorrenza di un entità con tipo e ID specifici.</span><span class="sxs-lookup"><span data-stu-id="9e494-304">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="9e494-305">Questo comportamento corrisponde a quello delle query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9e494-305">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="9e494-306">Ad esempio, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="9e494-306">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="9e494-307">restituisce la stessa istanza di `Category` per ogni `Product` associato alla categoria specificata.</span><span class="sxs-lookup"><span data-stu-id="9e494-307">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-308">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-308">New behavior</span></span>

<span data-ttu-id="9e494-309">A partire da EF Core 3.0, vengono create istanze di entità diverse quando un'entità con un determinato tipo e ID viene rilevata in posizioni diverse nel grafico restituito.</span><span class="sxs-lookup"><span data-stu-id="9e494-309">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="9e494-310">La query precedente, ad esempio, ora restituirà una nuova istanza di `Category` per ogni `Product` anche quando due prodotti sono associati alla stessa categoria.</span><span class="sxs-lookup"><span data-stu-id="9e494-310">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-311">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-311">Why</span></span>

<span data-ttu-id="9e494-312">La risoluzione delle identità (ovvero il processo per determinare che un'entità ha lo stesso tipo e ID di un'entità rilevata in precedenza) aggiunge un ulteriore sovraccarico della memoria e delle prestazioni,</span><span class="sxs-lookup"><span data-stu-id="9e494-312">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="9e494-313">il che va ad annullare il vantaggio derivante dall'uso delle query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9e494-313">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="9e494-314">Inoltre, anche se in alcuni casi la risoluzione delle identità può essere utile, tuttavia non è necessaria se le entità devono essere serializzate e inviate a un client, come avviene comunemente con le query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9e494-314">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-315">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-315">Mitigations</span></span>

<span data-ttu-id="9e494-316">Se la risoluzione delle identità è necessaria, usare una query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9e494-316">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="9e494-317">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="9e494-317">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="9e494-318">Problema n. 12378</span><span class="sxs-lookup"><span data-stu-id="9e494-318">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-319">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-319">Old behavior</span></span>

<span data-ttu-id="9e494-320">Nelle versioni precedenti a EF Core 3.0 i valori temporanei venivano assegnati a tutte le proprietà di chiave per cui veniva in seguito generato un valore reale dal database.</span><span class="sxs-lookup"><span data-stu-id="9e494-320">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="9e494-321">In genere questi valori temporanei erano numeri negativi elevati.</span><span class="sxs-lookup"><span data-stu-id="9e494-321">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-322">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-322">New behavior</span></span>

<span data-ttu-id="9e494-323">A partire dalla versione 3.0, EF Core archivia il valore di chiave temporaneo come parte delle informazioni di rilevamento dell'entità e non modifica la proprietà di chiave.</span><span class="sxs-lookup"><span data-stu-id="9e494-323">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-324">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-324">Why</span></span>

<span data-ttu-id="9e494-325">Questa modifica è stata apportata per impedire che i valori di chiave temporanei diventino erroneamente permanenti quando un'entità rilevata in precedenza da un'istanza `DbContext` viene spostata in un'altra istanza `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9e494-325">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-326">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-326">Mitigations</span></span>

<span data-ttu-id="9e494-327">Le applicazioni che assegnano valori di chiave primaria in chiavi esterne per creare associazioni tra le entità possono dipendere dal comportamento precedente se le chiavi primarie vengono generate dall'archivio e appartengono a entità con stato `Added`.</span><span class="sxs-lookup"><span data-stu-id="9e494-327">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="9e494-328">Questo può essere evitato:</span><span class="sxs-lookup"><span data-stu-id="9e494-328">This can be avoided by:</span></span>

* <span data-ttu-id="9e494-329">Non usando chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="9e494-329">Not using store-generated keys.</span></span>
* <span data-ttu-id="9e494-330">Impostando le proprietà di navigazione in modo da creare relazioni anziché impostando valori di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="9e494-330">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="9e494-331">Ottenendo i valori di chiave temporanei effettivi dalle informazioni di rilevamento dell'entità.</span><span class="sxs-lookup"><span data-stu-id="9e494-331">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="9e494-332">Ad esempio, `context.Entry(blog).Property(e => e.Id).CurrentValue` restituisce il valore temporaneo anche quando `blog.Id` non è stato impostato.</span><span class="sxs-lookup"><span data-stu-id="9e494-332">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="9e494-333">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="9e494-333">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="9e494-334">Problema n. 14616</span><span class="sxs-lookup"><span data-stu-id="9e494-334">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-335">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-335">Old behavior</span></span>

<span data-ttu-id="9e494-336">Nelle versioni precedenti a EF Core 3.0 un'entità non rilevata individuata da `DetectChanges` veniva rilevata nello stato `Added` e inserita come nuova riga quando veniva eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="9e494-336">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-337">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-337">New behavior</span></span>

<span data-ttu-id="9e494-338">A partire da EF Core 3.0, se un'entità usa valori di chiave generati e viene impostato un valore di chiave, l'entità viene rilevata nello stato `Modified`.</span><span class="sxs-lookup"><span data-stu-id="9e494-338">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="9e494-339">Ciò significa che si presuppone l'esistenza di una riga per l'entità che viene aggiornata quando viene eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="9e494-339">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="9e494-340">Se il valore di chiave non viene impostato o se il tipo di entità non usa chiavi generate, la nuova entità viene rilevata come `Added` come nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-340">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-341">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-341">Why</span></span>

<span data-ttu-id="9e494-342">Questa modifica è stata apportata per rendere più semplice e coerente l'uso di grafici di entità disconnesse con chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="9e494-342">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-343">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-343">Mitigations</span></span>

<span data-ttu-id="9e494-344">Questa modifica può interrompere un'applicazione se un tipo di entità è configurato per l'uso di chiavi generate ma i valori di chiave sono impostati in modo esplicito per le nuove istanze.</span><span class="sxs-lookup"><span data-stu-id="9e494-344">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="9e494-345">La correzione consiste nel configurare in modo esplicito le proprietà di chiave per non usare valori generati.</span><span class="sxs-lookup"><span data-stu-id="9e494-345">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="9e494-346">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="9e494-346">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="9e494-347">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="9e494-347">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="9e494-348">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-348">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="9e494-349">Problema n. 10114</span><span class="sxs-lookup"><span data-stu-id="9e494-349">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-350">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-350">Old behavior</span></span>

<span data-ttu-id="9e494-351">Nelle versioni precedenti alla versione 3.0, EF Core applicava azioni a catena (eliminazione delle entità dipendenti quando veniva eliminata un'entità di sicurezza obbligatoria o veniva recisa la relazione con un'entità di sicurezza obbligatoria) solo dopo la chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9e494-351">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-352">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-352">New behavior</span></span>

<span data-ttu-id="9e494-353">A partire dalla versione 3.0, EF Core applica le azioni a catena non appena viene rilevata la condizione di attivazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-353">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="9e494-354">Ad esempio, la chiamata a `context.Remove()` per eliminare un'entità di sicurezza causa anche l'impostazione immediata di tutti i dipendenti obbligatori correlati rilevati su `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="9e494-354">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-355">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-355">Why</span></span>

<span data-ttu-id="9e494-356">Questa modifica è stata apportata per migliorare l'esperienza di associazione di dati e degli scenari di controllo in cui è importante individuare le entità che verranno eliminate _prima della chiamata a_ `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="9e494-356">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-357">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-357">Mitigations</span></span>

<span data-ttu-id="9e494-358">Il comportamento precedente può essere ripristinato tramite le impostazioni in `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="9e494-358">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="9e494-359">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-359">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="9e494-360">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="9e494-360">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="9e494-361">Rilevamento del problema #18022</span><span class="sxs-lookup"><span data-stu-id="9e494-361">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-362">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-362">Old behavior</span></span>

<span data-ttu-id="9e494-363">Prima del 3,0, il caricamento con entusiasmo degli spostamenti delle raccolte tramite `Include` gli operatori causava la generazione di più query nel database relazionale, una per ogni tipo di entità correlato.</span><span class="sxs-lookup"><span data-stu-id="9e494-363">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-364">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-364">New behavior</span></span>

<span data-ttu-id="9e494-365">A partire da 3,0, EF Core genera una singola query con JOIN nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="9e494-365">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-366">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-366">Why</span></span>

<span data-ttu-id="9e494-367">L'invio di più query per l'implementazione di una singola query LINQ ha causato numerosi problemi, incluse le prestazioni negative in quanto sono stati necessari più round trip di database e i dati coerenza problemi poiché ogni query poteva osservare uno stato diverso del database.</span><span class="sxs-lookup"><span data-stu-id="9e494-367">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-368">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-368">Mitigations</span></span>

<span data-ttu-id="9e494-369">Sebbene tecnicamente non si tratti di una modifica di rilievo, potrebbe avere un impatto significativo sulle prestazioni dell'applicazione quando una singola query contiene un numero elevato di operatori per le esplorazioni della `Include` raccolta.</span><span class="sxs-lookup"><span data-stu-id="9e494-369">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="9e494-370">Per ulteriori informazioni e per riscrivere le query in modo più efficiente, [vedere il commento](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="9e494-370">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="9e494-371">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="9e494-371">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="9e494-372">Problema n. 12661</span><span class="sxs-lookup"><span data-stu-id="9e494-372">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-373">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-373">Old behavior</span></span>

<span data-ttu-id="9e494-374">Prima della versione 3.0, `DeleteBehavior.Restrict` creava chiavi esterne nel database con la semantica `Restrict`, ma modificava anche la correzione interna in modo non ovvio.</span><span class="sxs-lookup"><span data-stu-id="9e494-374">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-375">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-375">New behavior</span></span>

<span data-ttu-id="9e494-376">A partire dalla versione 3.0, `DeleteBehavior.Restrict` assicura che le chiavi esterne vengano create con la semantica `Restrict`, ovvero non a cascata e con generazione di un'eccezione in caso di violazione di vincolo, senza influire sulla correzione interna di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9e494-376">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-377">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-377">Why</span></span>

<span data-ttu-id="9e494-378">Questa modifica è stata apportata per migliorare l'esperienza di uso di `DeleteBehavior` in modo intuitivo, senza effetti collaterali imprevisti.</span><span class="sxs-lookup"><span data-stu-id="9e494-378">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-379">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-379">Mitigations</span></span>

<span data-ttu-id="9e494-380">Il comportamento precedente può essere ripristinato tramite `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="9e494-380">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="9e494-381">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="9e494-381">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="9e494-382">Problema n. 14194</span><span class="sxs-lookup"><span data-stu-id="9e494-382">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-383">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-383">Old behavior</span></span>

<span data-ttu-id="9e494-384">Nelle versioni precedenti a EF Core 3.0 i [tipi di query](xref:core/modeling/keyless-entity-types) erano uno strumento per eseguire query su dati che non definiscono una chiave primaria in modo strutturato.</span><span class="sxs-lookup"><span data-stu-id="9e494-384">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="9e494-385">Veniva infatti usato un tipo di query per eseguire il mapping di tipi di entità senza chiavi (più probabilmente da una vista, ma anche da una tabella), mentre veniva usato un tipo di entità normale quando era disponibile una chiave (più probabilmente da una tabella, ma anche da una vista).</span><span class="sxs-lookup"><span data-stu-id="9e494-385">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-386">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-386">New behavior</span></span>

<span data-ttu-id="9e494-387">Un tipo di query diventa ora semplicemente un tipo di entità senza chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="9e494-387">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="9e494-388">I tipi di entità senza chiave hanno la stessa funzionalità dei tipi di query nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-388">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-389">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-389">Why</span></span>

<span data-ttu-id="9e494-390">Questa modifica è stata apportata per ridurre la confusione riguardo lo scopo dei tipi di query.</span><span class="sxs-lookup"><span data-stu-id="9e494-390">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="9e494-391">In particolare, si tratta di tipi di entità senza chiave che sono intrinsecamente di sola lettura per questo motivo ma non dovrebbero essere usati solo perché un tipo di entità deve essere di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="9e494-391">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="9e494-392">Analogamente, spesso vengono mappati alle viste solo perché le viste spesso non definiscono le chiavi.</span><span class="sxs-lookup"><span data-stu-id="9e494-392">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-393">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-393">Mitigations</span></span>

<span data-ttu-id="9e494-394">Le parti dell'API seguenti sono ora obsolete:</span><span class="sxs-lookup"><span data-stu-id="9e494-394">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="9e494-395">**`ModelBuilder.Query<>()`** -È invece necessario `ModelBuilder.Entity<>().HasNoKey()` chiamare il metodo per contrassegnare un tipo di entità senza chiavi.</span><span class="sxs-lookup"><span data-stu-id="9e494-395">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="9e494-396">Non ne viene eseguita la configurazione per convenzione per evitare una configurazione errata quando è prevista una chiave primaria che tuttavia non corrisponde alla convenzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-396">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="9e494-397">**`DbQuery<>`** - `DbSet<>` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="9e494-397">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="9e494-398">**`DbContext.Query<>()`** - `DbContext.Set<>()` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="9e494-398">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="9e494-399">**`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="9e494-399">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="9e494-400">A causa di [un problema in 3. x](https://github.com/dotnet/efcore/issues/19537) durante l'esecuzione di query su entità senza chiave che dispongono di tutte le proprietà impostate su `null` un `null` verrà restituito al posto di un'entità, se il problema è applicabile anche allo scenario, aggiungere la logica per gestire `null` i risultati.</span><span class="sxs-lookup"><span data-stu-id="9e494-400">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="9e494-401">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="9e494-401">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="9e494-402">[Rilevamento del problema #12444](https://github.com/dotnet/efcore/issues/12444) 
 [Rilevamento del problema #9148](https://github.com/dotnet/efcore/issues/9148) 
 [Rilevamento del problema #14153](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="9e494-402">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="9e494-403">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-403">Old behavior</span></span>

<span data-ttu-id="9e494-404">Nelle versioni precedenti a EF Core 3.0 la configurazione della relazione di proprietà veniva eseguita direttamente dopo la chiamata a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="9e494-404">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-405">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-405">New behavior</span></span>

<span data-ttu-id="9e494-406">A partire da EF Core 3.0, è disponibile l'API Fluent per configurare una proprietà di navigazione per il proprietario usando `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="9e494-406">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="9e494-407">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-407">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="9e494-408">La configurazione correlata alla relazione tra proprietario e elemento di proprietà deve essere ora concatenata dopo `WithOwner()` in modo analogo a come vengono configurate altre relazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-408">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="9e494-409">La configurazione per il tipo di proprietà viene comunque concatenata dopo `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="9e494-409">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="9e494-410">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-410">For example:</span></span>

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

<span data-ttu-id="9e494-411">Inoltre, la chiamata a `Entity()`, `HasOne()` o `Set()` con una destinazione di tipo di proprietà genera ora un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9e494-411">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-412">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-412">Why</span></span>

<span data-ttu-id="9e494-413">Questa modifica è stata apportata per creare una separazione più netta tra la configurazione del tipo di proprietà e la _relazione_ con il tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-413">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="9e494-414">Ciò consente di eliminare ambiguità e confusione su metodi come `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="9e494-414">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-415">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-415">Mitigations</span></span>

<span data-ttu-id="9e494-416">Modificare la configurazione delle relazioni dei tipi di proprietà per usare la superficie della nuova API come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="9e494-416">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="9e494-417">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="9e494-417">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="9e494-418">Problema n. 9005</span><span class="sxs-lookup"><span data-stu-id="9e494-418">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-419">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-419">Old behavior</span></span>

<span data-ttu-id="9e494-420">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="9e494-420">Consider the following model:</span></span>

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

<span data-ttu-id="9e494-421">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o viene mappato in modo esplicito alla stessa tabella, era sempre necessaria un'istanza di `OrderDetails` per l'aggiunta di un nuovo `Order`.</span><span class="sxs-lookup"><span data-stu-id="9e494-421">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-422">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-422">New behavior</span></span>

<span data-ttu-id="9e494-423">A partire dalla versione 3.0, EF Core consente di aggiungere un `Order` senza un `OrderDetails` ed esegue il mapping di tutte le proprietà di `OrderDetails`, tranne che la chiave primaria, a colonne che ammettono valori Null.</span><span class="sxs-lookup"><span data-stu-id="9e494-423">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="9e494-424">In fase di query, EF Core imposta `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.</span><span class="sxs-lookup"><span data-stu-id="9e494-424">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-425">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-425">Mitigations</span></span>

<span data-ttu-id="9e494-426">Se il modello include una tabella condivisa dipendente con tutte le colonne facoltative, ma è previsto che la navigazione che punta a essa non sia `null`, l'applicazione deve essere modificata per gestire casi in cui la navigazione è `null`.</span><span class="sxs-lookup"><span data-stu-id="9e494-426">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="9e494-427">Se questo non è possibile, è consigliabile aggiungere una proprietà obbligatoria al tipo di entità o assegnare un valore non `null` ad almeno una proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-427">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="9e494-428">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="9e494-428">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="9e494-429">Problema n. 14154</span><span class="sxs-lookup"><span data-stu-id="9e494-429">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-430">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-430">Old behavior</span></span>

<span data-ttu-id="9e494-431">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="9e494-431">Consider the following model:</span></span>

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

<span data-ttu-id="9e494-432">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o è mappato in modo esplicito alla stessa tabella, il solo aggiornamento di `OrderDetails` non aggiornerà il valore `Version` nel client e l'aggiornamento successivo avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9e494-432">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-433">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-433">New behavior</span></span>

<span data-ttu-id="9e494-434">A partire dalla versione 3.0, EF Core propaga il nuovo valore `Version` a `Order` se è proprietario di `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="9e494-434">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="9e494-435">In caso contrario, viene generata un'eccezione durante la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="9e494-435">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-436">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-436">Why</span></span>

<span data-ttu-id="9e494-437">Questa modifica è stata apportata per evitare un valore del token di concorrenza non aggiornato quando viene aggiornata solo una delle entità mappate alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="9e494-437">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-438">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-438">Mitigations</span></span>

<span data-ttu-id="9e494-439">Tutte le entità che condividono la tabella devono includere una proprietà mappata alla colonna del token di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-439">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="9e494-440">È possibile crearne una in stato shadow:</span><span class="sxs-lookup"><span data-stu-id="9e494-440">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="9e494-441">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="9e494-441">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="9e494-442">Rilevamento del problema #18876</span><span class="sxs-lookup"><span data-stu-id="9e494-442">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-443">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-443">Old behavior</span></span>

<span data-ttu-id="9e494-444">Prima di EF Core 3,0, le entità di proprietà potevano essere sottoposte a query come qualsiasi altra navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-444">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="9e494-445">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-445">New behavior</span></span>

<span data-ttu-id="9e494-446">A partire da 3,0, EF Core genererà un'operazione se una query di rilevamento proietta un'entità di proprietà senza proprietario.</span><span class="sxs-lookup"><span data-stu-id="9e494-446">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-447">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-447">Why</span></span>

<span data-ttu-id="9e494-448">Le entità di proprietà non possono essere modificate senza il proprietario, quindi nella maggior parte dei casi l'esecuzione di query in questo modo è un errore.</span><span class="sxs-lookup"><span data-stu-id="9e494-448">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-449">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-449">Mitigations</span></span>

<span data-ttu-id="9e494-450">Se l'entità di proprietà deve essere rilevata in modo da essere modificata in un secondo momento, il proprietario deve essere incluso nella query.</span><span class="sxs-lookup"><span data-stu-id="9e494-450">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="9e494-451">In caso contrario `AsNoTracking()` , aggiungere una chiamata:</span><span class="sxs-lookup"><span data-stu-id="9e494-451">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="9e494-452">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="9e494-452">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="9e494-453">Problema n. 13998</span><span class="sxs-lookup"><span data-stu-id="9e494-453">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-454">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-454">Old behavior</span></span>

<span data-ttu-id="9e494-455">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="9e494-455">Consider the following model:</span></span>

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

<span data-ttu-id="9e494-456">Prima di EF Core 3.0, per la proprietà `ShippingAddress` sarebbe stato eseguito il mapping a colonne separate per `BulkOrder` e `Order` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9e494-456">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-457">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-457">New behavior</span></span>

<span data-ttu-id="9e494-458">A partire dalla versione 3.0, EF Core crea solo una colonna per `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="9e494-458">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-459">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-459">Why</span></span>

<span data-ttu-id="9e494-460">Il comportamento precedente non era previsto.</span><span class="sxs-lookup"><span data-stu-id="9e494-460">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-461">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-461">Mitigations</span></span>

<span data-ttu-id="9e494-462">È ancora possibile eseguire il mapping esplicito della proprietà a una colonna separata per i tipi derivati:</span><span class="sxs-lookup"><span data-stu-id="9e494-462">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="9e494-463">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="9e494-463">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="9e494-464">Problema n. 13274</span><span class="sxs-lookup"><span data-stu-id="9e494-464">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-465">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-465">Old behavior</span></span>

<span data-ttu-id="9e494-466">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="9e494-466">Consider the following model:</span></span>

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

<span data-ttu-id="9e494-467">Nelle versioni precedenti a EF Core 3.0 veniva usata la proprietà `CustomerId` per la chiave esterna per convenzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-467">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="9e494-468">Tuttavia, se `Order` è un tipo di proprietà, `CustomerId` sarebbe la chiave primaria e ciò non è in genere auspicabile.</span><span class="sxs-lookup"><span data-stu-id="9e494-468">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-469">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-469">New behavior</span></span>

<span data-ttu-id="9e494-470">A partire da 3.0, EF Core non tenta di usare le proprietà per le chiavi esterne per convenzione se hanno lo stesso nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="9e494-470">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="9e494-471">Viene ancora eseguita la corrispondenza tra i criteri del nome del tipo dell'entità di sicurezza concatenato al nome della proprietà dell'entità di sicurezza e il nome di navigazione concatenato al nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="9e494-471">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="9e494-472">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-472">For example:</span></span>

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

#### <a name="why"></a><span data-ttu-id="9e494-473">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-473">Why</span></span>

<span data-ttu-id="9e494-474">Questa modifica è stata apportata per evitare una definizione errata della proprietà di chiave primaria nel tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-474">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-475">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-475">Mitigations</span></span>

<span data-ttu-id="9e494-476">Se la proprietà è stata progettata per essere la chiave esterna e di conseguenza parte della chiave primaria, configurarla in modo esplicito come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="9e494-476">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="9e494-477">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="9e494-477">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="9e494-478">Problema n. 14218</span><span class="sxs-lookup"><span data-stu-id="9e494-478">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-479">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-479">Old behavior</span></span>

<span data-ttu-id="9e494-480">Prima di EF Core 3.0, se il contesto apre la connessione all'interno di un `TransactionScope`, la connessione rimane aperta mentre è attivo il `TransactionScope` corrente.</span><span class="sxs-lookup"><span data-stu-id="9e494-480">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="9e494-481">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-481">New behavior</span></span>

<span data-ttu-id="9e494-482">A partire dalla versione 3.0, EF Core chiude la connessione non appena non viene più usata.</span><span class="sxs-lookup"><span data-stu-id="9e494-482">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-483">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-483">Why</span></span>

<span data-ttu-id="9e494-484">Questa modifica consente di usare più contesti nello stesso `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="9e494-484">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="9e494-485">Il nuovo comportamento corrisponde anche a EF6.</span><span class="sxs-lookup"><span data-stu-id="9e494-485">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-486">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-486">Mitigations</span></span>

<span data-ttu-id="9e494-487">Se la connessione deve rimanere aperta, la chiamata esplicita di `OpenConnection()` garantirà che EF Core non la chiuda prematuramente:</span><span class="sxs-lookup"><span data-stu-id="9e494-487">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="9e494-488">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="9e494-488">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="9e494-489">Problema n. 6872</span><span class="sxs-lookup"><span data-stu-id="9e494-489">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-490">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-490">Old behavior</span></span>

<span data-ttu-id="9e494-491">Nelle versioni precedenti a EF Core 3.0 veniva usato un unico generatore di valori condiviso per tutte le proprietà di chiavi di tipo intero in memoria.</span><span class="sxs-lookup"><span data-stu-id="9e494-491">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-492">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-492">New behavior</span></span>

<span data-ttu-id="9e494-493">A partire da EF Core 3.0, ogni proprietà di chiave di tipo intero riceve un generatore di valori quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9e494-493">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="9e494-494">Inoltre, se il database viene eliminato, la generazione di chiavi viene reimpostata per tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="9e494-494">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-495">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-495">Why</span></span>

<span data-ttu-id="9e494-496">Questa modifica è stata apportata per allineare maggiormente la generazione di chiavi in memoria alla generazione di chiavi del database reale e per migliorare la possibilità di isolare i test l'uno dall'altro quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9e494-496">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-497">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-497">Mitigations</span></span>

<span data-ttu-id="9e494-498">Ciò può interrompere un'applicazione che si basa sull'impostazione di valori di chiave in memoria specifici.</span><span class="sxs-lookup"><span data-stu-id="9e494-498">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="9e494-499">È consigliabile non basare l'applicazione su valori di chiave specifici o eseguire l'aggiornamento per passare al nuovo comportamento.</span><span class="sxs-lookup"><span data-stu-id="9e494-499">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="9e494-500">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-500">Backing fields are used by default</span></span>

[<span data-ttu-id="9e494-501">Problema n. 12430</span><span class="sxs-lookup"><span data-stu-id="9e494-501">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-502">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-502">Old behavior</span></span>

<span data-ttu-id="9e494-503">Nelle versioni precedenti alla versione 3.0, anche se il campo sottostante di una proprietà era noto, per impostazione predefinita EF Core eseguiva la lettura e la scrittura del valore della proprietà usando i metodi getter e setter della proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-503">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="9e494-504">L'eccezione era costituita dall'esecuzione di query in cui il campo sottostante, se noto, veniva impostato direttamente.</span><span class="sxs-lookup"><span data-stu-id="9e494-504">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-505">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-505">New behavior</span></span>

<span data-ttu-id="9e494-506">A partire da EF Core 3.0, se il campo sottostante di una proprietà è noto, la lettura e la scrittura della proprietà vengono sempre eseguite usando il campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="9e494-506">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="9e494-507">Ciò potrebbe causare un'interruzione dell'applicazione se l'applicazione si basa su un comportamento aggiuntivo codificato nei metodi getter o setter.</span><span class="sxs-lookup"><span data-stu-id="9e494-507">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-508">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-508">Why</span></span>

<span data-ttu-id="9e494-509">Questa modifica è stata apportata per impedire a EF Core di attivare per errore la logica di business per impostazione predefinita quando si eseguono operazioni di database che interessano le entità.</span><span class="sxs-lookup"><span data-stu-id="9e494-509">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-510">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-510">Mitigations</span></span>

<span data-ttu-id="9e494-511">È possibile ripristinare il comportamento delle versioni precedenti alla versione 3.0 tramite la configurazione della modalità di accesso delle proprietà in `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9e494-511">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="9e494-512">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-512">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="9e494-513">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="9e494-513">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="9e494-514">Problema n. 12523</span><span class="sxs-lookup"><span data-stu-id="9e494-514">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-515">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-515">Old behavior</span></span>

<span data-ttu-id="9e494-516">Nelle versioni precedenti a EF Core 3.0, se più campi soddisfacevano le regole di ricerca del campo sottostante di una proprietà, veniva selezionato un solo campo in base a un ordine di precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-516">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="9e494-517">Ciò poteva causare l'uso di un campo non corretto nei casi ambigui.</span><span class="sxs-lookup"><span data-stu-id="9e494-517">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-518">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-518">New behavior</span></span>

<span data-ttu-id="9e494-519">A partire da EF Core 3.0, se più campi corrispondono alla stessa proprietà, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9e494-519">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-520">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-520">Why</span></span>

<span data-ttu-id="9e494-521">Questa modifica è stata apportata per evitare di usare automaticamente un campo rispetto a un altro quando un solo campo può essere quello corretto.</span><span class="sxs-lookup"><span data-stu-id="9e494-521">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-522">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-522">Mitigations</span></span>

<span data-ttu-id="9e494-523">Per le proprietà con campi sottostanti ambigui, il campo da usare deve essere specificato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="9e494-523">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="9e494-524">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="9e494-524">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="9e494-525">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="9e494-525">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="9e494-526">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-526">Old behavior</span></span>

<span data-ttu-id="9e494-527">Prima di EF Core 3,0, una proprietà può essere specificata da un valore stringa e, se non è stata trovata alcuna proprietà con tale nome nel tipo .NET, EF Core tenterà di associarla a un campo usando le regole di convenzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-527">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="9e494-528">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-528">New behavior</span></span>

<span data-ttu-id="9e494-529">A partire da EF Core 3.0, una proprietà solo campo deve corrispondere esattamente al nome del campo.</span><span class="sxs-lookup"><span data-stu-id="9e494-529">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="9e494-530">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-530">Why</span></span>

<span data-ttu-id="9e494-531">Questa modifica è stata introdotta per evitare di usare lo stesso campo per due proprietà con nome simile. Le regole di corrispondenza per le proprietà solo campo sono state anche uniformate a quelle per le proprietà mappate a proprietà CLR.</span><span class="sxs-lookup"><span data-stu-id="9e494-531">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-532">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-532">Mitigations</span></span>

<span data-ttu-id="9e494-533">Le proprietà solo campo devono avere lo stesso nome del campo a cui vengono mappate.</span><span class="sxs-lookup"><span data-stu-id="9e494-533">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="9e494-534">In una versione futura di EF Core dopo il 3,0, si prevede di abilitare di nuovo in modo esplicito il nome di un campo diverso dal nome della proprietà (vedere il problema [#15307](https://github.com/dotnet/efcore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="9e494-534">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="9e494-535">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="9e494-535">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="9e494-536">Problema n. 14756</span><span class="sxs-lookup"><span data-stu-id="9e494-536">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-537">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-537">Old behavior</span></span>

<span data-ttu-id="9e494-538">Prima di EF Core 3,0, chiamando `AddDbContext` o `AddDbContextPool` sarebbero stati registrati anche i servizi di memorizzazione nella cache e di registrazione della memoria con le chiamate a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) e [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="9e494-538">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-539">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-539">New behavior</span></span>

<span data-ttu-id="9e494-540">A partire da EF Core 3.0, `AddDbContext` e `AddDbContextPool` non registreranno più questi servizi con inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9e494-540">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-541">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-541">Why</span></span>

<span data-ttu-id="9e494-542">EF Core 3.0 non richiede che questi servizi siano inclusi nel contenitore di inserimento delle dipendenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-542">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="9e494-543">Tuttavia, se `ILoggerFactory` è registrato nel contenitore di inserimento delle dipendenze dell'applicazione, verrà ancora usato da EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-543">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-544">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-544">Mitigations</span></span>

<span data-ttu-id="9e494-545">Se l'applicazione necessita di questi servizi, registrarli in modo esplicito con il contenitore di inserimento delle dipendenze usando [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="9e494-545">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="9e494-546">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="9e494-546">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="9e494-547">Rilevamento del problema #12905</span><span class="sxs-lookup"><span data-stu-id="9e494-547">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-548">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-548">Old behavior</span></span>

<span data-ttu-id="9e494-549">Prima di EF Core 3,0, i metodi di chiamata `AddEntityFramework*` registravano anche i servizi di memorizzazione nella cache di memoria con di senza un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-549">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-550">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-550">New behavior</span></span>

<span data-ttu-id="9e494-551">A partire da EF Core 3,0, registrerà `AddEntityFramework*` un servizio IMemoryCache con un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-551">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="9e494-552">Se altri servizi aggiunti successivamente dipendono da IMemoryCache, è possibile raggiungere rapidamente il limite predefinito che causa eccezioni o prestazioni ridotte.</span><span class="sxs-lookup"><span data-stu-id="9e494-552">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-553">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-553">Why</span></span>

<span data-ttu-id="9e494-554">L'utilizzo di IMemoryCache senza un limite può comportare l'utilizzo di memoria non controllata se è presente un bug nella logica di memorizzazione nella cache delle query o se le query vengono generate in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="9e494-554">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="9e494-555">La presenza di un limite predefinito attenua un potenziale attacco DoS.</span><span class="sxs-lookup"><span data-stu-id="9e494-555">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-556">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-556">Mitigations</span></span>

<span data-ttu-id="9e494-557">Nella maggior parte dei casi, la chiamata a `AddEntityFramework*` non è necessaria se `AddDbContext` `AddDbContextPool` viene chiamato anche o.</span><span class="sxs-lookup"><span data-stu-id="9e494-557">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="9e494-558">Pertanto, la migliore mitigazione consiste nel rimuovere la `AddEntityFramework*` chiamata.</span><span class="sxs-lookup"><span data-stu-id="9e494-558">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="9e494-559">Se l'applicazione necessita di questi servizi, registrare un'implementazione di IMemoryCache in modo esplicito con il contenitore DI inserimento in anticipo usando [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="9e494-559">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="9e494-560">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="9e494-560">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="9e494-561">Problema n. 13552</span><span class="sxs-lookup"><span data-stu-id="9e494-561">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-562">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-562">Old behavior</span></span>

<span data-ttu-id="9e494-563">Nelle versioni precedenti a EF Core 3.0 la chiamata a `DbContext.Entry` causava il rilevamento delle modifiche per tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="9e494-563">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="9e494-564">Ciò garantiva l'aggiornamento dello stato esposto in `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="9e494-564">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-565">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-565">New behavior</span></span>

<span data-ttu-id="9e494-566">A partire da EF Core 3.0, la chiamata a `DbContext.Entry` causa ora solo il tentativo di rilevare le modifiche nell'entità specificata e in tutte le relative entità di sicurezza rilevate.</span><span class="sxs-lookup"><span data-stu-id="9e494-566">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="9e494-567">Ciò significa che le modifiche apportate altrove potrebbero non essere state rilevate tramite la chiamata al metodo e ciò potrebbe avere implicazioni sullo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-567">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="9e494-568">Si noti che se `ChangeTracker.AutoDetectChangesEnabled` è impostato su `false`, verrà disabilitato anche questo tipo di rilevamento delle modifiche locali.</span><span class="sxs-lookup"><span data-stu-id="9e494-568">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="9e494-569">Gli altri metodi che causano il rilevamento delle modifiche, ad esempio `ChangeTracker.Entries` e `SaveChanges`, causano ancora un `DetectChanges` completo di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="9e494-569">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-570">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-570">Why</span></span>

<span data-ttu-id="9e494-571">Questa modifica è stata apportata per migliorare le prestazioni predefinite dell'uso di `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="9e494-571">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-572">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-572">Mitigations</span></span>

<span data-ttu-id="9e494-573">Chiamare `ChangeTracker.DetectChanges()` in modo esplicito prima di chiamare `Entry` per garantire il comportamento precedente alla versione 3.0.</span><span class="sxs-lookup"><span data-stu-id="9e494-573">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="9e494-574">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-574">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="9e494-575">Problema n. 14617</span><span class="sxs-lookup"><span data-stu-id="9e494-575">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-576">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-576">Old behavior</span></span>

<span data-ttu-id="9e494-577">Nelle versioni precedenti a EF Core 3.0 le proprietà di chiave `string` e `byte[]` potevano essere usate senza impostare in modo esplicito un valore non Null.</span><span class="sxs-lookup"><span data-stu-id="9e494-577">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="9e494-578">In questi casi, il valore di chiave veniva generato nel client come GUID, serializzato in byte per `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="9e494-578">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-579">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-579">New behavior</span></span>

<span data-ttu-id="9e494-580">A partire da EF Core 3.0 viene generata un'eccezione che indica che non è stato impostato alcun valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="9e494-580">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-581">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-581">Why</span></span>

<span data-ttu-id="9e494-582">Questa modifica è stata apportata poiché i valori `string`/`byte[]` generati dal client non risultano in genere utili e il comportamento predefinito rendeva complesso ragionare sui valori di chiave generati in un modo comune.</span><span class="sxs-lookup"><span data-stu-id="9e494-582">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-583">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-583">Mitigations</span></span>

<span data-ttu-id="9e494-584">È possibile ripristinare il comportamento precedente alla versione 3.0 specificando in modo esplicito che le proprietà di chiave devono usare i valori generati se non viene impostato alcun altro valore non Null.</span><span class="sxs-lookup"><span data-stu-id="9e494-584">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="9e494-585">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="9e494-585">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="9e494-586">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="9e494-586">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="9e494-587">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="9e494-587">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="9e494-588">Problema n. 14698</span><span class="sxs-lookup"><span data-stu-id="9e494-588">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-589">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-589">Old behavior</span></span>

<span data-ttu-id="9e494-590">Nelle versioni precedenti a EF Core 3.0 `ILoggerFactory` veniva registrato come servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="9e494-590">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-591">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-591">New behavior</span></span>

<span data-ttu-id="9e494-592">A partire da EF Core 3.0, `ILoggerFactory` viene registrato come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="9e494-592">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-593">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-593">Why</span></span>

<span data-ttu-id="9e494-594">Questa modifica è stata apportata per consentire l'associazione di un logger a un'istanza `DbContext` che abilita altre funzionalità e rimuove alcuni casi di comportamento anomalo, ad esempio un'esplosione dei provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="9e494-594">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-595">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-595">Mitigations</span></span>

<span data-ttu-id="9e494-596">Questa modifica non dovrebbe influire sul codice dell'applicazione a meno che non vengano registrati e usati servizi personalizzati nel provider di servizi interno di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-596">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="9e494-597">Questo non è un comportamento comune.</span><span class="sxs-lookup"><span data-stu-id="9e494-597">This isn't common.</span></span>
<span data-ttu-id="9e494-598">In questi casi la maggior parte delle operazioni continuano a essere eseguite correttamente, ma qualsiasi servizio singleton dipendente da `ILoggerFactory` dovrà essere modificato per ottenere `ILoggerFactory` in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="9e494-598">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="9e494-599">Se si verificano situazioni simili, inviare una segnalazione nello [strumento di gestione dei problemi in GitHub relativo a EF Core](https://github.com/dotnet/efcore/issues) per comunicare in che modo viene usato `ILoggerFactory` per consentirci di comprendere meglio come evitare ulteriori interruzioni in futuro.</span><span class="sxs-lookup"><span data-stu-id="9e494-599">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="9e494-600">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="9e494-600">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="9e494-601">Problema n. 12780</span><span class="sxs-lookup"><span data-stu-id="9e494-601">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-602">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-602">Old behavior</span></span>

<span data-ttu-id="9e494-603">Nelle versioni precedenti a EF Core 3.0, quando `DbContext` veniva eliminato non esisteva alcun metodo per scoprire se una determinata proprietà di navigazione in un'entità ottenuta da un contesto specifico veniva caricata completamente o meno.</span><span class="sxs-lookup"><span data-stu-id="9e494-603">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="9e494-604">I proxy presupponevano invece che venisse caricata una navigazione di riferimento se era presente un valore non Null e che venisse caricata una navigazione di raccolta se era presente un valore.</span><span class="sxs-lookup"><span data-stu-id="9e494-604">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="9e494-605">In questi casi il tentativo di eseguire un caricamento lazy non avrebbe avuto alcun esito.</span><span class="sxs-lookup"><span data-stu-id="9e494-605">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-606">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-606">New behavior</span></span>

<span data-ttu-id="9e494-607">A partire da Entity Framework Core 3.0, i proxy tengono traccia del caricamento o mancato caricamento di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-607">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="9e494-608">Ciò significa che il tentativo di accedere a una proprietà di navigazione caricata dopo l'eliminazione del contesto non avrà mai alcun esito, anche quando la navigazione caricata è vuota o ha valore Null.</span><span class="sxs-lookup"><span data-stu-id="9e494-608">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="9e494-609">Al contrario, il tentativo di accedere a una proprietà di navigazione non caricata genera un'eccezione se il contesto viene eliminato anche se la proprietà di navigazione è una raccolta non vuota.</span><span class="sxs-lookup"><span data-stu-id="9e494-609">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="9e494-610">Se si verifica questa situazione significa che il codice dell'applicazione sta tentando di usare il caricamento lazy in un momento non valido e l'applicazione deve essere modificata in modo da non eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-610">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-611">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-611">Why</span></span>

<span data-ttu-id="9e494-612">Questa modifica è stata apportata per rendere coerente e corretto il comportamento durante un tentativo di caricamento lazy in un'istanza `DbContext` eliminata.</span><span class="sxs-lookup"><span data-stu-id="9e494-612">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-613">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-613">Mitigations</span></span>

<span data-ttu-id="9e494-614">Aggiornare il codice dell'applicazione per fare in modo che non venga tentato il caricamento lazy con un contesto eliminato oppure specificare una configurazione in modo che non venga eseguita alcuna operazione come descritto nel messaggio di eccezione.</span><span class="sxs-lookup"><span data-stu-id="9e494-614">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="9e494-615">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="9e494-615">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="9e494-616">Problema n. 10236</span><span class="sxs-lookup"><span data-stu-id="9e494-616">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-617">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-617">Old behavior</span></span>

<span data-ttu-id="9e494-618">Nelle versioni precedenti a EF Core 3.0 veniva registrato un avviso per le applicazioni che creavano un numero eccessivo di provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="9e494-618">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-619">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-619">New behavior</span></span>

<span data-ttu-id="9e494-620">A partire da EF Core 3.0, l'avviso viene considerato un errore e viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9e494-620">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-621">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-621">Why</span></span>

<span data-ttu-id="9e494-622">Questa modifica è stata apportata per gestire meglio il codice dell'applicazione tramite un'esposizione più esplicita di questa situazione di errore.</span><span class="sxs-lookup"><span data-stu-id="9e494-622">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-623">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-623">Mitigations</span></span>

<span data-ttu-id="9e494-624">L'azione più appropriata quando si verifica questo errore consiste nell'individuare la causa radice e nell'interrompere la creazione di numerosi provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="9e494-624">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="9e494-625">È possibile tuttavia convertire nuovamente l'errore in avviso o ignorarlo tramite una configurazione in `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9e494-625">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="9e494-626">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-626">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="9e494-627">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="9e494-627">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="9e494-628">Problema n. 9171</span><span class="sxs-lookup"><span data-stu-id="9e494-628">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-629">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-629">Old behavior</span></span>

<span data-ttu-id="9e494-630">Prima di EF Core 3.0, il codice che chiama `HasOne` o `HasMany` con una singola stringa era interpretato in modo poco chiaro.</span><span class="sxs-lookup"><span data-stu-id="9e494-630">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="9e494-631">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-631">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="9e494-632">Apparentemente, il codice mette in relazione `Samurai` con un altro tipo di entità tramite la proprietà di navigazione `Entrance`, che può essere privata.</span><span class="sxs-lookup"><span data-stu-id="9e494-632">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="9e494-633">In realtà, il codice tenta di creare una relazione con un tipo di entità denominato `Entrance` senza proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-633">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-634">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-634">New behavior</span></span>

<span data-ttu-id="9e494-635">A partire da EF Core 3.0, il codice sopra riportato ora esegue quello che avrebbe dovuto fare in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-635">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-636">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-636">Why</span></span>

<span data-ttu-id="9e494-637">Il comportamento precedente era molto poco chiaro, soprattutto durante la lettura del codice di configurazione e la ricerca di errori.</span><span class="sxs-lookup"><span data-stu-id="9e494-637">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-638">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-638">Mitigations</span></span>

<span data-ttu-id="9e494-639">Questa modifica causerà problemi solo nelle applicazioni che configurano relazioni in modo esplicito usando stringhe per i nomi dei tipi e senza specificare in modo esplicito la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-639">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="9e494-640">Non è uno scenario comune.</span><span class="sxs-lookup"><span data-stu-id="9e494-640">This is not common.</span></span>
<span data-ttu-id="9e494-641">Il comportamento precedente può essere ottenuto passando esplicitamente `null` per il nome della proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-641">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="9e494-642">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-642">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="9e494-643">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="9e494-643">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="9e494-644">Problema n. 15184</span><span class="sxs-lookup"><span data-stu-id="9e494-644">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-645">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-645">Old behavior</span></span>

<span data-ttu-id="9e494-646">I metodi asincroni seguenti in precedenza restituivano il tipo `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="9e494-646">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="9e494-647">`ValueGenerator.NextValueAsync()` (e classi derivate)</span><span class="sxs-lookup"><span data-stu-id="9e494-647">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-648">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-648">New behavior</span></span>

<span data-ttu-id="9e494-649">I metodi indicati in precedenza ora restituiscono il tipo `ValueTask<T>` sullo stesso `T` come in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-649">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-650">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-650">Why</span></span>

<span data-ttu-id="9e494-651">Questa modifica riduce il numero delle allocazioni di heap sostenute quando si richiamano questi metodi, con un miglioramento generale delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-651">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-652">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-652">Mitigations</span></span>

<span data-ttu-id="9e494-653">Le applicazioni semplicemente in attesa delle API precedenti devono solo essere ricompilate e non sono richieste modifiche del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="9e494-653">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="9e494-654">Per scenari di utilizzo più complessi (ad esempio, il passaggio del tipo `Task` restituito a `Task.WhenAny()`) è richiesto in genere che il tipo `ValueTask<T>` restituito venga convertito in `Task<T>` chiamando `AsTask()` su di esso.</span><span class="sxs-lookup"><span data-stu-id="9e494-654">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="9e494-655">Si noti che in questo modo si annulla la riduzione delle allocazioni consentita da questa modifica.</span><span class="sxs-lookup"><span data-stu-id="9e494-655">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="9e494-656">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="9e494-656">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="9e494-657">Problema n. 9913</span><span class="sxs-lookup"><span data-stu-id="9e494-657">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-658">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-658">Old behavior</span></span>

<span data-ttu-id="9e494-659">Il nome di annotazione delle annotazioni di mapping del tipo era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="9e494-659">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-660">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-660">New behavior</span></span>

<span data-ttu-id="9e494-661">Il nome di annotazione delle annotazioni di mapping del tipo è ora "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="9e494-661">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-662">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-662">Why</span></span>

<span data-ttu-id="9e494-663">Il mapping dei tipi non viene più usato solo per i provider di database relazionali.</span><span class="sxs-lookup"><span data-stu-id="9e494-663">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-664">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-664">Mitigations</span></span>

<span data-ttu-id="9e494-665">Ciò causa un'interruzione solo nelle applicazioni che accedono al mapping dei tipi direttamente come annotazione. Questa situazione non è comune.</span><span class="sxs-lookup"><span data-stu-id="9e494-665">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="9e494-666">L'azione più appropriata per risolvere il problema consiste nell'usare la superficie dell'API per accedere al mapping dei tipi anziché l'annotazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-666">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="9e494-667">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="9e494-667">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="9e494-668">Problema n. 11811</span><span class="sxs-lookup"><span data-stu-id="9e494-668">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-669">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-669">Old behavior</span></span>

<span data-ttu-id="9e494-670">Nelle versioni precedenti a EF Core 3.0, la chiamata a `ToTable()` in un tipo derivato veniva ignorata poiché soltanto la strategia di mapping dell'ereditarietà era una tabella per gerarchia dove la chiamata non era valida.</span><span class="sxs-lookup"><span data-stu-id="9e494-670">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-671">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-671">New behavior</span></span>

<span data-ttu-id="9e494-672">A partire da EF Core 3.0 e in preparazione all'aggiunta del supporto per la tabella per tipo e per TPC in una versione successiva, la chiamata a `ToTable()` in un tipo derivato genera un'eccezione per evitare una modifica del mapping imprevista in futuro.</span><span class="sxs-lookup"><span data-stu-id="9e494-672">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-673">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-673">Why</span></span>

<span data-ttu-id="9e494-674">Attualmente il mapping di un tipo derivato in una tabella diversa non è un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="9e494-674">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="9e494-675">Questa modifica consente di evitare interruzioni future quando l'operazione diventerà un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="9e494-675">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-676">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-676">Mitigations</span></span>

<span data-ttu-id="9e494-677">Rimuovere qualsiasi tentativo di mapping di tipi derivati in altre tabelle.</span><span class="sxs-lookup"><span data-stu-id="9e494-677">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="9e494-678">ForSqlServerHasIndex sostituito con HasIndex</span><span class="sxs-lookup"><span data-stu-id="9e494-678">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="9e494-679">Problema n. 12366</span><span class="sxs-lookup"><span data-stu-id="9e494-679">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-680">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-680">Old behavior</span></span>

<span data-ttu-id="9e494-681">Nelle versioni precedenti a EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offriva un metodo per configurare le colonne usate con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="9e494-681">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-682">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-682">New behavior</span></span>

<span data-ttu-id="9e494-683">A partire da EF Core 3.0, l'uso di `Include` in un indice è supportato a livello relazionale.</span><span class="sxs-lookup"><span data-stu-id="9e494-683">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="9e494-684">Usare `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="9e494-684">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-685">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-685">Why</span></span>

<span data-ttu-id="9e494-686">Questa modifica è stata apportata per consolidare l'API per gli indici con `Include` in un'unica posizione per tutti i provider di database.</span><span class="sxs-lookup"><span data-stu-id="9e494-686">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-687">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-687">Mitigations</span></span>

<span data-ttu-id="9e494-688">Usare la nuova API, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-688">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="9e494-689">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="9e494-689">Metadata API changes</span></span>

[<span data-ttu-id="9e494-690">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="9e494-690">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="9e494-691">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-691">New behavior</span></span>

<span data-ttu-id="9e494-692">Le proprietà seguenti sono state convertite in metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="9e494-692">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="9e494-693">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-693">Why</span></span>

<span data-ttu-id="9e494-694">Questa modifica semplifica l'implementazione delle interfacce menzionate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-694">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-695">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-695">Mitigations</span></span>

<span data-ttu-id="9e494-696">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="9e494-696">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="9e494-697">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="9e494-697">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="9e494-698">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="9e494-698">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="9e494-699">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-699">New behavior</span></span>

<span data-ttu-id="9e494-700">I metodi di estensione specifici del provider verranno resi flat:</span><span class="sxs-lookup"><span data-stu-id="9e494-700">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="9e494-701">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-701">Why</span></span>

<span data-ttu-id="9e494-702">Questa modifica semplifica l'implementazione dei metodi di estensione menzionati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9e494-702">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-703">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-703">Mitigations</span></span>

<span data-ttu-id="9e494-704">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="9e494-704">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="9e494-705">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-705">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="9e494-706">Problema n. 12151</span><span class="sxs-lookup"><span data-stu-id="9e494-706">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-707">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-707">Old behavior</span></span>

<span data-ttu-id="9e494-708">Nelle versioni precedenti a EF Core 3.0, EF Core inviava `PRAGMA foreign_keys = 1` quando veniva aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="9e494-708">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-709">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-709">New behavior</span></span>

<span data-ttu-id="9e494-710">A partire da EF Core 3.0, EF Core non invia più `PRAGMA foreign_keys = 1` quando viene aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="9e494-710">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-711">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-711">Why</span></span>

<span data-ttu-id="9e494-712">Questa modifica è stata apportata poiché EF Core usa `SQLitePCLRaw.bundle_e_sqlite3` per impostazione predefinita. Ciò significa che l'imposizione della chiave esterna è abilitata per impostazione predefinita e non deve essere abilitata in modo esplicito ogni volta che viene aperta una connessione.</span><span class="sxs-lookup"><span data-stu-id="9e494-712">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-713">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-713">Mitigations</span></span>

<span data-ttu-id="9e494-714">Le chiavi esterne sono abilitate per impostazione predefinita in SQLitePCLRaw.bundle_e_sqlite3, usato per impostazione predefinita per EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-714">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="9e494-715">Per gli altri casi, è possibile abilitare le chiavi esterne specificando `Foreign Keys=True` nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="9e494-715">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="9e494-716">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="9e494-716">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="9e494-717">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-717">Old behavior</span></span>

<span data-ttu-id="9e494-718">Nelle versioni precedenti a EF Core 3.0, EF Core usava `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="9e494-718">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-719">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-719">New behavior</span></span>

<span data-ttu-id="9e494-720">A partire da EF Core 3.0, EF Core usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="9e494-720">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-721">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-721">Why</span></span>

<span data-ttu-id="9e494-722">Questa modifica è stata apportata per rendere coerente la versione di SQLite usata in iOS con le altre piattaforme.</span><span class="sxs-lookup"><span data-stu-id="9e494-722">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-723">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-723">Mitigations</span></span>

<span data-ttu-id="9e494-724">Per usare la versione di SQLite nativa in iOS, configurare `Microsoft.Data.Sqlite` per l'uso di un'aggregazione `SQLitePCLRaw` diversa.</span><span class="sxs-lookup"><span data-stu-id="9e494-724">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="9e494-725">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-725">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="9e494-726">Problema n. 15078</span><span class="sxs-lookup"><span data-stu-id="9e494-726">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-727">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-727">Old behavior</span></span>

<span data-ttu-id="9e494-728">I valori Guid in precedenza venivano archiviati come valori BLOB in SQLite.</span><span class="sxs-lookup"><span data-stu-id="9e494-728">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-729">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-729">New behavior</span></span>

<span data-ttu-id="9e494-730">I valori Guid vengono ora archiviati come TEXT.</span><span class="sxs-lookup"><span data-stu-id="9e494-730">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-731">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-731">Why</span></span>

<span data-ttu-id="9e494-732">Il formato binario dei valori Guid non è standardizzato.</span><span class="sxs-lookup"><span data-stu-id="9e494-732">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="9e494-733">L'archiviazione dei valori come TEXT rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="9e494-733">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-734">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-734">Mitigations</span></span>

<span data-ttu-id="9e494-735">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="9e494-735">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="9e494-736">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-736">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="9e494-737">Microsoft.Data.Sqlite rimane in grado di leggere i valori Guid sia da colonne BLOB che TEXT. Tuttavia, poiché è stato modificato il formato predefinito per i parametri e le costanti, probabilmente sarà necessario intervenire per la maggior parte degli scenari che coinvolgono valori Guid.</span><span class="sxs-lookup"><span data-stu-id="9e494-737">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="9e494-738">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="9e494-738">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="9e494-739">Problema n. 15020</span><span class="sxs-lookup"><span data-stu-id="9e494-739">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-740">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-740">Old behavior</span></span>

<span data-ttu-id="9e494-741">I valori char in precedenza venivano archiviati come valori interi in SQLite.</span><span class="sxs-lookup"><span data-stu-id="9e494-741">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="9e494-742">Un valore char di *A* veniva ad esempio archiviato come valore intero 65.</span><span class="sxs-lookup"><span data-stu-id="9e494-742">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-743">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-743">New behavior</span></span>

<span data-ttu-id="9e494-744">I valori char vengono ora archiviati come testo.</span><span class="sxs-lookup"><span data-stu-id="9e494-744">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-745">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-745">Why</span></span>

<span data-ttu-id="9e494-746">L'archiviazione dei valori come testo è un'operazione più naturale e rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="9e494-746">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-747">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-747">Mitigations</span></span>

<span data-ttu-id="9e494-748">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="9e494-748">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="9e494-749">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e494-749">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="9e494-750">Microsoft.Data.Sqlite rimane comunque in grado di leggere i valori di caratteri presenti sia nelle colonne di valori interi sia in quelle di testo, quindi alcuni scenari potrebbero non richiedere alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="9e494-750">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="9e494-751">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="9e494-751">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="9e494-752">Problema n. 12978</span><span class="sxs-lookup"><span data-stu-id="9e494-752">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-753">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-753">Old behavior</span></span>

<span data-ttu-id="9e494-754">Gli ID di migrazione venivano inavvertitamente generati usando il calendario delle impostazioni cultura correnti.</span><span class="sxs-lookup"><span data-stu-id="9e494-754">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-755">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-755">New behavior</span></span>

<span data-ttu-id="9e494-756">Gli ID di migrazione ora vengono sempre generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica (calendario gregoriano).</span><span class="sxs-lookup"><span data-stu-id="9e494-756">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-757">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-757">Why</span></span>

<span data-ttu-id="9e494-758">L'ordine delle migrazioni è importante quando si esegue l'aggiornamento del database o si risolvono i conflitti di unione.</span><span class="sxs-lookup"><span data-stu-id="9e494-758">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="9e494-759">L'uso del calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica evita i problemi che possono verificarsi quando i membri del team hanno calendari di sistema diversi.</span><span class="sxs-lookup"><span data-stu-id="9e494-759">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-760">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-760">Mitigations</span></span>

<span data-ttu-id="9e494-761">Questa modifica interessa gli utenti che usano un calendario non gregoriano in cui l'anno ha un'estensione superiore al calendario gregoriano (come il calendario buddista tailandese).</span><span class="sxs-lookup"><span data-stu-id="9e494-761">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="9e494-762">Gli ID di migrazione esistenti dovranno essere aggiornati in modo che le nuove migrazioni vengano collocate dopo le migrazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-762">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="9e494-763">L'ID di migrazione è disponibile nell'attributo di migrazione presente nei file di progettazione delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-763">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="9e494-764">È necessario aggiornare anche la tabella della cronologia delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-764">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="9e494-765">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="9e494-765">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="9e494-766">Problema n. 16400</span><span class="sxs-lookup"><span data-stu-id="9e494-766">Tracking Issue #16400</span></span>](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-767">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-767">Old behavior</span></span>

<span data-ttu-id="9e494-768">Prima di EF Core 3.0 si poteva usare `UseRowNumberForPaging` per generare codice SQL per la suddivisione in pagine compatibile con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="9e494-768">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-769">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-769">New behavior</span></span>

<span data-ttu-id="9e494-770">A partire da EF Core 3.0, EF genererà solo codice SQL per la suddivisione in pagine compatibile solo con versioni successive di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9e494-770">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-771">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-771">Why</span></span>

<span data-ttu-id="9e494-772">Questa modifica è stata apportata perché [SQL Server 2008 non è più un prodotto supportato](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) e l'aggiornamento di questa funzionalità per interagire con le modifiche apportate per le query in EF Core 3.0 è un lavoro significativo.</span><span class="sxs-lookup"><span data-stu-id="9e494-772">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-773">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-773">Mitigations</span></span>

<span data-ttu-id="9e494-774">È consigliabile eseguire l'aggiornamento a una versione più recente di SQL Server o usare un livello di compatibilità superiore, in modo che il codice SQL generato sia supportato.</span><span class="sxs-lookup"><span data-stu-id="9e494-774">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="9e494-775">Detto questo, se non è possibile procedere in questo modo, [aggiungere un commento per il problema](https://github.com/dotnet/efcore/issues/16400) con indicazioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="9e494-775">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="9e494-776">Microsoft potrebbe rivedere questa decisione in base ai commenti e suggerimenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-776">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="9e494-777">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="9e494-777">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="9e494-778">Problema n. 16119</span><span class="sxs-lookup"><span data-stu-id="9e494-778">Tracking Issue #16119</span></span>](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-779">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-779">Old behavior</span></span>

<span data-ttu-id="9e494-780">`IDbContextOptionsExtension` conteneva metodi per fornire i metadati relativi all'estensione.</span><span class="sxs-lookup"><span data-stu-id="9e494-780">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-781">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-781">New behavior</span></span>

<span data-ttu-id="9e494-782">Questi metodi sono stati spostati in una nuova classe di base astratta `DbContextOptionsExtensionInfo`, restituita da una nuova proprietà `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="9e494-782">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-783">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-783">Why</span></span>

<span data-ttu-id="9e494-784">Nelle versioni dalla 2.0 alla 3.0 è stato necessario aggiungere o modificare questi metodi più volte.</span><span class="sxs-lookup"><span data-stu-id="9e494-784">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="9e494-785">Suddividendoli in una nuova classe di base astratta sarà più facile apportare questo tipo di modifiche senza compromettere il funzionamento delle estensioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-785">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-786">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-786">Mitigations</span></span>

<span data-ttu-id="9e494-787">Aggiornare le estensioni per seguire il nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="9e494-787">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="9e494-788">Sono disponibili esempi nelle numerose implementazioni di `IDbContextOptionsExtension` per diversi tipi di estensioni nel codice sorgente di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-788">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="9e494-789">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="9e494-789">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="9e494-790">Problema n. 10985</span><span class="sxs-lookup"><span data-stu-id="9e494-790">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="9e494-791">Modifica</span><span class="sxs-lookup"><span data-stu-id="9e494-791">Change</span></span>

<span data-ttu-id="9e494-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` è stato rinominato in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="9e494-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-793">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-793">Why</span></span>

<span data-ttu-id="9e494-794">Allineamento del nome di questo evento di avviso con tutti gli altri eventi di avviso.</span><span class="sxs-lookup"><span data-stu-id="9e494-794">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-795">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-795">Mitigations</span></span>

<span data-ttu-id="9e494-796">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="9e494-796">Use the new name.</span></span> <span data-ttu-id="9e494-797">(Si noti che il numero di ID evento non è stato modificato.)</span><span class="sxs-lookup"><span data-stu-id="9e494-797">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="9e494-798">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="9e494-798">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="9e494-799">Problema n. 10730</span><span class="sxs-lookup"><span data-stu-id="9e494-799">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-800">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-800">Old behavior</span></span>

<span data-ttu-id="9e494-801">Prima di EF Core 3.0, si faceva riferimento ai nomi di vincolo di chiave esterna semplicemente con "Name".</span><span class="sxs-lookup"><span data-stu-id="9e494-801">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="9e494-802">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-802">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="9e494-803">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-803">New behavior</span></span>

<span data-ttu-id="9e494-804">A partire da EF Core 3.0, si fa ora riferimento ai nomi di vincolo di chiave esterna con "ConstraintName".</span><span class="sxs-lookup"><span data-stu-id="9e494-804">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="9e494-805">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-805">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="9e494-806">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-806">Why</span></span>

<span data-ttu-id="9e494-807">Questa modifica introduce coerenza per la denominazione in quest'area e chiarisce anche che si tratta del nome del vincolo di chiave esterna e non del nome della colonna o della proprietà per cui è definita la chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="9e494-807">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-808">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-808">Mitigations</span></span>

<span data-ttu-id="9e494-809">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="9e494-809">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="9e494-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="9e494-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="9e494-811">Problema n. 15997</span><span class="sxs-lookup"><span data-stu-id="9e494-811">Tracking Issue #15997</span></span>](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-812">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-812">Old behavior</span></span>

<span data-ttu-id="9e494-813">Prima di EF Core 3.0 questi metodi erano protetti.</span><span class="sxs-lookup"><span data-stu-id="9e494-813">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-814">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-814">New behavior</span></span>

<span data-ttu-id="9e494-815">A partire da EF Core 3.0 questi metodi sono pubblici.</span><span class="sxs-lookup"><span data-stu-id="9e494-815">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-816">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-816">Why</span></span>

<span data-ttu-id="9e494-817">Questi metodi vengono usati da EF per determinare se un database viene creato, ma vuoto.</span><span class="sxs-lookup"><span data-stu-id="9e494-817">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="9e494-818">Ciò risulta utile all'esterno di EF quando occorre determinare se applicare o meno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-818">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-819">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-819">Mitigations</span></span>

<span data-ttu-id="9e494-820">Modificare l'accessibilità di eventuali override.</span><span class="sxs-lookup"><span data-stu-id="9e494-820">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="9e494-821">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="9e494-821">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="9e494-822">Problema n. 11506</span><span class="sxs-lookup"><span data-stu-id="9e494-822">Tracking Issue #11506</span></span>](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-823">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-823">Old behavior</span></span>

<span data-ttu-id="9e494-824">Prima di EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un pacchetto NuGet normale ed era possibile fare riferimento al relativo assembly dai progetti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="9e494-824">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-825">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-825">New behavior</span></span>

<span data-ttu-id="9e494-826">A partire da EF Core 3.0 è un pacchetto DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="9e494-826">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="9e494-827">Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che non è più possibile, per impostazione predefinita, fare riferimento al relativo assembly.</span><span class="sxs-lookup"><span data-stu-id="9e494-827">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-828">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-828">Why</span></span>

<span data-ttu-id="9e494-829">Questo pacchetto è destinato solo all'uso in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-829">This package is only intended to be used at design time.</span></span> <span data-ttu-id="9e494-830">Le applicazioni distribuite non devono farvi riferimento.</span><span class="sxs-lookup"><span data-stu-id="9e494-830">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="9e494-831">Questa raccomandazione è rafforzata dall'impostazione del pacchetto come DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="9e494-831">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-832">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-832">Mitigations</span></span>

<span data-ttu-id="9e494-833">Se è necessario fare riferimento a questo pacchetto per eseguire l'override del comportamento della fase di progettazione di EF Core, è possibile aggiornare i metadati dell'elemento PackageReference nel progetto.</span><span class="sxs-lookup"><span data-stu-id="9e494-833">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="9e494-834">In presenza di riferimenti transitivi al pacchetto tramite Microsoft.EntityFrameworkCore.Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto per modificare i relativi metadati.</span><span class="sxs-lookup"><span data-stu-id="9e494-834">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="9e494-835">Un riferimento esplicito di questo tipo deve essere aggiunto a qualsiasi progetto in cui sono necessari i tipi del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="9e494-835">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="9e494-836">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="9e494-836">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="9e494-837">Problema n. 14824</span><span class="sxs-lookup"><span data-stu-id="9e494-837">Tracking Issue #14824</span></span>](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-838">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-838">Old behavior</span></span>

<span data-ttu-id="9e494-839">Microsoft.EntityFrameworkCore.Sqlite dipendeva in precedenza dalla versione 1.1.12 di SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="9e494-839">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-840">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-840">New behavior</span></span>

<span data-ttu-id="9e494-841">Il pacchetto è stato aggiornato in base alla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="9e494-841">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-842">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-842">Why</span></span>

<span data-ttu-id="9e494-843">La versione 2.0.0 di SQLitePCL.raw è destinata a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="9e494-843">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="9e494-844">Era in precedenza destinata a .NET Standard 1.1 e ciò richiedeva un notevole impegno di chiusura di pacchetti transitivi per il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="9e494-844">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-845">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-845">Mitigations</span></span>

<span data-ttu-id="9e494-846">SQLitePCL.raw versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-846">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="9e494-847">Per informazioni dettagliate, vedere le [Note sulla versione](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .</span><span class="sxs-lookup"><span data-stu-id="9e494-847">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="9e494-848">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="9e494-848">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="9e494-849">Problema n. 14825</span><span class="sxs-lookup"><span data-stu-id="9e494-849">Tracking Issue #14825</span></span>](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-850">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-850">Old behavior</span></span>

<span data-ttu-id="9e494-851">I pacchetti spaziali dipendevano in precedenza dalla versione 1.15.1 di NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="9e494-851">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-852">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-852">New behavior</span></span>

<span data-ttu-id="9e494-853">Il pacchetto è stato aggiornato in modo da dipendere dalla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="9e494-853">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-854">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-854">Why</span></span>

<span data-ttu-id="9e494-855">La versione 2.0.0 di NetTopologySuite risolve vari problemi di usabilità riscontrati dagli utenti di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9e494-855">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-856">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-856">Mitigations</span></span>

<span data-ttu-id="9e494-857">NetTopologySuite versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="9e494-857">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="9e494-858">Per informazioni dettagliate, vedere le [Note sulla versione](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .</span><span class="sxs-lookup"><span data-stu-id="9e494-858">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="9e494-859">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="9e494-859">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="9e494-860">Rilevamento del problema #15636</span><span class="sxs-lookup"><span data-stu-id="9e494-860">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-861">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-861">Old behavior</span></span>

<span data-ttu-id="9e494-862">Microsoft. EntityFrameworkCore. SqlServer in precedenza dipende da System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="9e494-862">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9e494-863">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-863">New behavior</span></span>

<span data-ttu-id="9e494-864">Il pacchetto è stato aggiornato in base a Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="9e494-864">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-865">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-865">Why</span></span>

<span data-ttu-id="9e494-866">Microsoft. Data. SqlClient è il driver di accesso ai dati principale per SQL Server in futuro e System. Data. SqlClient non è più l'obiettivo dello sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9e494-866">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="9e494-867">Alcune funzionalità importanti, ad esempio Always Encrypted, sono disponibili solo in Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="9e494-867">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-868">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-868">Mitigations</span></span>

<span data-ttu-id="9e494-869">Se il codice prende una dipendenza diretta da System. Data. SqlClient, è necessario modificarlo in modo che faccia riferimento a Microsoft. Data. SqlClient. Poiché i due pacchetti gestiscono un livello molto elevato di compatibilità API, questo dovrebbe essere solo una semplice modifica del pacchetto e dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="9e494-869">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="9e494-870">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="9e494-870">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="9e494-871">Problema n. 13573</span><span class="sxs-lookup"><span data-stu-id="9e494-871">Tracking Issue #13573</span></span>](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-872">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-872">Old behavior</span></span>

<span data-ttu-id="9e494-873">Un tipo di entità con più proprietà di navigazione unidirezionale che fanno riferimento a se stesse e più chiavi esterne corrispondenti è stato erroneamente configurato come relazione singola.</span><span class="sxs-lookup"><span data-stu-id="9e494-873">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="9e494-874">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-874">For example:</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="9e494-875">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-875">New behavior</span></span>

<span data-ttu-id="9e494-876">Questo scenario viene ora rilevato nella compilazione del modello e viene generata un'eccezione indicante che il modello è ambiguo.</span><span class="sxs-lookup"><span data-stu-id="9e494-876">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-877">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-877">Why</span></span>

<span data-ttu-id="9e494-878">Il modello risultante era ambiguo e sarà probabilmente errato in questo caso.</span><span class="sxs-lookup"><span data-stu-id="9e494-878">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-879">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-879">Mitigations</span></span>

<span data-ttu-id="9e494-880">Usare la configurazione completa della relazione.</span><span class="sxs-lookup"><span data-stu-id="9e494-880">Use full configuration of the relationship.</span></span> <span data-ttu-id="9e494-881">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9e494-881">For example:</span></span>

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

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="9e494-882">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="9e494-882">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="9e494-883">Rilevamento del problema #12757</span><span class="sxs-lookup"><span data-stu-id="9e494-883">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="9e494-884">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="9e494-884">Old behavior</span></span>

<span data-ttu-id="9e494-885">Un DbFunction configurato con schema come stringa vuota è stato trattato come funzione predefinita senza uno schema.</span><span class="sxs-lookup"><span data-stu-id="9e494-885">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="9e494-886">Il codice seguente, ad esempio, eseguirà il mapping della `DatePart` funzione CLR alla `DATEPART` funzione predefinita in SqlServer.</span><span class="sxs-lookup"><span data-stu-id="9e494-886">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="9e494-887">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="9e494-887">New behavior</span></span>

<span data-ttu-id="9e494-888">Tutti i mapping di DbFunction sono considerati mappati alle funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9e494-888">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="9e494-889">Pertanto, il valore stringa vuoto inserisce la funzione all'interno dello schema predefinito per il modello.</span><span class="sxs-lookup"><span data-stu-id="9e494-889">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="9e494-890">Che può corrispondere allo schema configurato in modo esplicito tramite l'API Fluent `modelBuilder.HasDefaultSchema()` o `dbo` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="9e494-890">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="9e494-891">Perché</span><span class="sxs-lookup"><span data-stu-id="9e494-891">Why</span></span>

<span data-ttu-id="9e494-892">Lo schema precedentemente vuoto era un modo per trattare la funzione è incorporata, ma tale logica è applicabile solo per SqlServer, in cui le funzioni predefinite non appartengono ad alcuno schema.</span><span class="sxs-lookup"><span data-stu-id="9e494-892">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9e494-893">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="9e494-893">Mitigations</span></span>

<span data-ttu-id="9e494-894">Configurare manualmente la conversione di DbFunction per eseguirne il mapping a una funzione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9e494-894">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="9e494-895">~~EF Core 3,0 destinazioni .NET Standard 2,1 anziché .NET Standard 2,0~~ Ripristinato</span><span class="sxs-lookup"><span data-stu-id="9e494-895">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

[<span data-ttu-id="9e494-896">Problema n. 15498</span><span class="sxs-lookup"><span data-stu-id="9e494-896">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

<span data-ttu-id="9e494-897">EF Core 3,0 destinazioni .NET Standard 2,1, che è una modifica di rilievo che esclude .NET Framework applicazioni.</span><span class="sxs-lookup"><span data-stu-id="9e494-897">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="9e494-898">EF Core 3,1 è stato ripristinato e le destinazioni .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="9e494-898">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="9e494-899">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="9e494-899">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="9e494-900">Problema n. 14523</span><span class="sxs-lookup"><span data-stu-id="9e494-900">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="9e494-901">Questa modifica è stata annullata perché la nuova configurazione in EF Core 3.0 consente all'applicazione di specificare il livello di log per qualsiasi evento.</span><span class="sxs-lookup"><span data-stu-id="9e494-901">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="9e494-902">Ad esempio, per impostare la registrazione di SQL sul livello `Debug`, configurare il livello in modo esplicito in `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="9e494-902">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
