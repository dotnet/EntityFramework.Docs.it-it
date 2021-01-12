---
title: Modifiche di rilievo in EF Core 3. x-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 8c0be4193c79e838e40bfc2dc10c9d12b01381cd
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128771"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="babf2-103">Modifiche di rilievo incluse in EF Core 3. x</span><span class="sxs-lookup"><span data-stu-id="babf2-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="babf2-104">Le modifiche alle API e al comportamento seguenti possono causare l'interruzione delle applicazioni esistenti durante l'aggiornamento a 3. x.</span><span class="sxs-lookup"><span data-stu-id="babf2-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="babf2-105">Le modifiche che si prevede abbiano impatto solo sui provider di database sono documentate nelle [modifiche che influiscono sul provider](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="babf2-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="babf2-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="babf2-106">Summary</span></span>

| <span data-ttu-id="babf2-107">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="babf2-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="babf2-108">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="babf2-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="babf2-109">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="babf2-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="babf2-110">Alto</span><span class="sxs-lookup"><span data-stu-id="babf2-110">High</span></span>       |
| [<span data-ttu-id="babf2-111">Lo strumento da riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="babf2-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="babf2-112">Alto</span><span class="sxs-lookup"><span data-stu-id="babf2-112">High</span></span>      |
| [<span data-ttu-id="babf2-113">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="babf2-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="babf2-114">Alto</span><span class="sxs-lookup"><span data-stu-id="babf2-114">High</span></span>      |
| [<span data-ttu-id="babf2-115">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="babf2-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="babf2-116">Alto</span><span class="sxs-lookup"><span data-stu-id="babf2-116">High</span></span>      |
| [<span data-ttu-id="babf2-117">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="babf2-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="babf2-118">Alto</span><span class="sxs-lookup"><span data-stu-id="babf2-118">High</span></span>      |
| [<span data-ttu-id="babf2-119">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babf2-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="babf2-120">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-120">Medium</span></span>      |
| [<span data-ttu-id="babf2-121">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="babf2-122">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-122">Medium</span></span>      |
| [<span data-ttu-id="babf2-123">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="babf2-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="babf2-124">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-124">Medium</span></span>      |
| [<span data-ttu-id="babf2-125">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="babf2-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="babf2-126">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-126">Medium</span></span>      |
| [<span data-ttu-id="babf2-127">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="babf2-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="babf2-128">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-128">Medium</span></span>      |
| [<span data-ttu-id="babf2-129">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="babf2-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="babf2-130">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-130">Medium</span></span>      |
| [<span data-ttu-id="babf2-131">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="babf2-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="babf2-132">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-132">Medium</span></span>      |
| [<span data-ttu-id="babf2-133">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="babf2-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="babf2-134">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-134">Medium</span></span>      |
| [<span data-ttu-id="babf2-135">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="babf2-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="babf2-136">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-136">Medium</span></span>      |
| [<span data-ttu-id="babf2-137">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="babf2-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="babf2-138">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-138">Medium</span></span>      |
| [<span data-ttu-id="babf2-139">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="babf2-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="babf2-140">Media</span><span class="sxs-lookup"><span data-stu-id="babf2-140">Medium</span></span>      |
| [<span data-ttu-id="babf2-141">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="babf2-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="babf2-142">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-142">Low</span></span>      |
| [<span data-ttu-id="babf2-143">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="babf2-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="babf2-144">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-144">Low</span></span>      |
| [<span data-ttu-id="babf2-145">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="babf2-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="babf2-146">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-146">Low</span></span>      |
| [<span data-ttu-id="babf2-147">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="babf2-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="babf2-148">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-148">Low</span></span>      |
| [<span data-ttu-id="babf2-149">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="babf2-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="babf2-150">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-150">Low</span></span>      |
| [<span data-ttu-id="babf2-151">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="babf2-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="babf2-152">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-152">Low</span></span>      |
| [<span data-ttu-id="babf2-153">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="babf2-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="babf2-154">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-154">Low</span></span>      |
| [<span data-ttu-id="babf2-155">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="babf2-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="babf2-156">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-156">Low</span></span>      |
| [<span data-ttu-id="babf2-157">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="babf2-158">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-158">Low</span></span>      |
| [<span data-ttu-id="babf2-159">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="babf2-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="babf2-160">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-160">Low</span></span>      |
| [<span data-ttu-id="babf2-161">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="babf2-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="babf2-162">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-162">Low</span></span>      |
| [<span data-ttu-id="babf2-163">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="babf2-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="babf2-164">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-164">Low</span></span>      |
| [<span data-ttu-id="babf2-165">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="babf2-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="babf2-166">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-166">Low</span></span>      |
| [<span data-ttu-id="babf2-167">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="babf2-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="babf2-168">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-168">Low</span></span>      |
| [<span data-ttu-id="babf2-169">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="babf2-170">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-170">Low</span></span>      |
| [<span data-ttu-id="babf2-171">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="babf2-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="babf2-172">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-172">Low</span></span>      |
| [<span data-ttu-id="babf2-173">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="babf2-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="babf2-174">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-174">Low</span></span>      |
| [<span data-ttu-id="babf2-175">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="babf2-176">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-176">Low</span></span>      |
| [<span data-ttu-id="babf2-177">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="babf2-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="babf2-178">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-178">Low</span></span>      |
| [<span data-ttu-id="babf2-179">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="babf2-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="babf2-180">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-180">Low</span></span>      |
| [<span data-ttu-id="babf2-181">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="babf2-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="babf2-182">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-182">Low</span></span>      |
| [<span data-ttu-id="babf2-183">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="babf2-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="babf2-184">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-184">Low</span></span>      |
| [<span data-ttu-id="babf2-185">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="babf2-186">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-186">Low</span></span>      |
| [<span data-ttu-id="babf2-187">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="babf2-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="babf2-188">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-188">Low</span></span>      |
| [<span data-ttu-id="babf2-189">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="babf2-190">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-190">Low</span></span>      |
| [<span data-ttu-id="babf2-191">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="babf2-192">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-192">Low</span></span>      |
| [<span data-ttu-id="babf2-193">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="babf2-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="babf2-194">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-194">Low</span></span>      |
| [<span data-ttu-id="babf2-195">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="babf2-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="babf2-196">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-196">Low</span></span>      |
| [<span data-ttu-id="babf2-197">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="babf2-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="babf2-198">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-198">Low</span></span>      |
| [<span data-ttu-id="babf2-199">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="babf2-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="babf2-200">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-200">Low</span></span>      |
| [<span data-ttu-id="babf2-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="babf2-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="babf2-202">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-202">Low</span></span>      |
| [<span data-ttu-id="babf2-203">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="babf2-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="babf2-204">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-204">Low</span></span>      |
| [<span data-ttu-id="babf2-205">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="babf2-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="babf2-206">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-206">Low</span></span>      |
| [<span data-ttu-id="babf2-207">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="babf2-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="babf2-208">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-208">Low</span></span>      |
| [<span data-ttu-id="babf2-209">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="babf2-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="babf2-210">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-210">Low</span></span>      |
| [<span data-ttu-id="babf2-211">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="babf2-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="babf2-212">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-212">Low</span></span>      |
| [<span data-ttu-id="babf2-213">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="babf2-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="babf2-214">Basso</span><span class="sxs-lookup"><span data-stu-id="babf2-214">Low</span></span>      |
| [<span data-ttu-id="babf2-215">~~EF Core 3,0 destinazioni .NET Standard 2,1 anziché .NET Standard 2,0~~ Ripristinato</span><span class="sxs-lookup"><span data-stu-id="babf2-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="babf2-216">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="babf2-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="babf2-217">Modifiche a elevato effetto</span><span class="sxs-lookup"><span data-stu-id="babf2-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="babf2-218">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="babf2-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="babf2-219">[Rilevamento del problema #14935](https://github.com/dotnet/efcore/issues/14935) 
 [Vedere anche problema #12795](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="babf2-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="babf2-220">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-220">Old behavior</span></span>

<span data-ttu-id="babf2-221">Nelle versioni precedenti alla versione 3.0, quando EF Core non era in grado di convertire un'espressione inclusa in una query in SQL o in un parametro, l'espressione veniva automaticamente valutata nel client.</span><span class="sxs-lookup"><span data-stu-id="babf2-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="babf2-222">Per impostazione predefinita, la valutazione client di espressioni potenzialmente dispendiose si limitava ad attivare solo un avviso.</span><span class="sxs-lookup"><span data-stu-id="babf2-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-223">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-223">New behavior</span></span>

<span data-ttu-id="babf2-224">A partire dalla versione 3.0, EF Core consente solo la valutazione delle espressioni nella proiezione di primo livello (l'ultima chiamata `Select()` nella query) nel client.</span><span class="sxs-lookup"><span data-stu-id="babf2-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="babf2-225">Quando le espressioni in altre posizioni all'interno della query non possono essere convertite in SQL o in un parametro, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="babf2-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-226">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-226">Why</span></span>

<span data-ttu-id="babf2-227">La valutazione client automatica delle query consente di eseguire numerose query anche nel caso in cui parti importanti delle query non possono essere convertite.</span><span class="sxs-lookup"><span data-stu-id="babf2-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="babf2-228">Questo comportamento può causare un comportamento imprevisto e potenzialmente dannoso che può diventare evidente solo in produzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="babf2-229">Ad esempio, una condizione in una chiamata `Where()` che non può essere convertita può causare il trasferimento di tutte le righe della tabella del server di database e l'applicazione del filtro nel client.</span><span class="sxs-lookup"><span data-stu-id="babf2-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="babf2-230">È probabile che questa situazione non venga rilevata se la tabella contiene solo alcune righe in fase di sviluppo, ma che abbia un grande impatto quando l'applicazione passa in produzione dove la tabella può contenere milioni di righe.</span><span class="sxs-lookup"><span data-stu-id="babf2-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="babf2-231">Gli avvisi di valutazione client inoltre si sono rivelati molto facili da ignorare durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="babf2-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="babf2-232">Inoltre, la valutazione client automatica può causare problemi in cui il miglioramento della conversione di query per espressioni specifiche causa modifiche impreviste che causano un'interruzione da una versione all'altra.</span><span class="sxs-lookup"><span data-stu-id="babf2-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-233">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-233">Mitigations</span></span>

<span data-ttu-id="babf2-234">Se una query non può essere convertita completamente, riscrivere la query in un formato che possa essere convertito o usare `AsEnumerable()`, `ToList()` o un elemento simile per riportare in modo esplicito i dati nel client dove possono essere quindi ulteriormente elaborati usando LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="babf2-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="babf2-235">Modifiche a media Impact</span><span class="sxs-lookup"><span data-stu-id="babf2-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="babf2-236">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babf2-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="babf2-237">Annunci problema n. 325</span><span class="sxs-lookup"><span data-stu-id="babf2-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-238">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-238">Old behavior</span></span>

<span data-ttu-id="babf2-239">Nelle versioni precedenti ad ASP.NET Core 3.0, quando si aggiungeva un riferimento a un pacchetto in `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, veniva inserito EF Core e alcuni dei provider di dati di EF Core come il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="babf2-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-240">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-240">New behavior</span></span>

<span data-ttu-id="babf2-241">A partire dalla versione 3.0, il framework condiviso di ASP.NET Core non include EF Core o provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-242">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-242">Why</span></span>

<span data-ttu-id="babf2-243">Prima di questa modifica, per ottenere EF Core erano necessarie procedure diverse, a seconda che l'applicazione avesse o meno come destinazione ASP.NET Core e SQL Server.</span><span class="sxs-lookup"><span data-stu-id="babf2-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="babf2-244">Inoltre, l'aggiornamento di ASP.NET Core forzava l'aggiornamento di EF Core e del provider di SQL Server, non sempre auspicabile.</span><span class="sxs-lookup"><span data-stu-id="babf2-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="babf2-245">Con questa modifica, la procedura per ottenere EF Core è la stessa in tutti i provider, le implementazioni .NET supportate e i tipi di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="babf2-246">Gli sviluppatori possono ora controllare anche in modo preciso quando vengono aggiornati EF Core e i provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-247">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-247">Mitigations</span></span>

<span data-ttu-id="babf2-248">Per usare EF Core in un'applicazione ASP.NET Core 3.0 o in un'altra applicazione supportata, aggiungere in modo esplicito un riferimento al pacchetto al provider di database di EF Core che verrà usato dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="babf2-249">Lo strumento della riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="babf2-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="babf2-250">Problema n. 14016</span><span class="sxs-lookup"><span data-stu-id="babf2-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-251">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-251">Old behavior</span></span>

<span data-ttu-id="babf2-252">Prima della versione 3.0, lo strumento `dotnet ef` era incluso in .NET Core SDK ed era immediatamente disponibile dalla riga di comando di qualsiasi progetto senza richiedere passaggi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="babf2-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-253">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-253">New behavior</span></span>

<span data-ttu-id="babf2-254">A partire dalla versione 3.0, .NET SDK non include lo strumento `dotnet ef` pertanto, prima di poterlo usare, è necessario installarlo in modo esplicito come strumento locale o globale.</span><span class="sxs-lookup"><span data-stu-id="babf2-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-255">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-255">Why</span></span>

<span data-ttu-id="babf2-256">Questa modifica consente di distribuire e aggiornare `dotnet ef` come uno strumento della riga di comando di .NET in NuGet, coerentemente con il fatto che anche EF Core 3.0 viene distribuito come pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="babf2-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-257">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-257">Mitigations</span></span>

<span data-ttu-id="babf2-258">Per essere in grado di gestire le migrazioni o eseguire lo scaffolding di `DbContext`, installare `dotnet-ef` come strumento globale:</span><span class="sxs-lookup"><span data-stu-id="babf2-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="babf2-259">È inoltre possibile ottenerlo come strumento locale quando si ripristinano le dipendenze di un progetto che lo dichiara come dipendenza di strumenti utilizzando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="babf2-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="babf2-260">Modifiche a basso effetto</span><span class="sxs-lookup"><span data-stu-id="babf2-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="babf2-261">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="babf2-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="babf2-262">Problema n. 10996</span><span class="sxs-lookup"><span data-stu-id="babf2-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

> [!IMPORTANT]
> <span data-ttu-id="babf2-263">`ExecuteSqlCommand` e `ExecuteSqlCommandAsync` sono deprecati.</span><span class="sxs-lookup"><span data-stu-id="babf2-263">`ExecuteSqlCommand` and `ExecuteSqlCommandAsync` are deprecated.</span></span> <span data-ttu-id="babf2-264">Usare invece questi metodi.</span><span class="sxs-lookup"><span data-stu-id="babf2-264">Use these methods instead.</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="babf2-265">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-265">Old behavior</span></span>

<span data-ttu-id="babf2-266">Prima di EF Core 3.0, erano disponibili overload per questi nomi di metodo per supportare l'uso con una stringa normale o una stringa che deve essere interpolata in SQL e parametri.</span><span class="sxs-lookup"><span data-stu-id="babf2-266">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-267">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-267">New behavior</span></span>

<span data-ttu-id="babf2-268">A partire da EF Core 3.0, usare `FromSqlRaw`, `ExecuteSqlRaw` e `ExecuteSqlRawAsync` per creare una query con parametri in cui i parametri vengono passati separatamente dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="babf2-268">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="babf2-269">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-269">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="babf2-270">Usare `FromSqlInterpolated`, `ExecuteSqlInterpolated`, e `ExecuteSqlInterpolatedAsync` per creare una query con parametri in cui i parametri vengono passati come parte di una stringa di query interpolata.</span><span class="sxs-lookup"><span data-stu-id="babf2-270">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="babf2-271">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-271">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="babf2-272">Si noti che entrambe le query precedenti produrranno lo stesso codice SQL con parametri con gli stessi parametri SQL.</span><span class="sxs-lookup"><span data-stu-id="babf2-272">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-273">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-273">Why</span></span>

<span data-ttu-id="babf2-274">Con gli overload di metodi come questi, è molto facile chiamare accidentalmente il metodo con stringa non elaborata anche se l'intento era chiamare il metodo con stringa interpolata e viceversa.</span><span class="sxs-lookup"><span data-stu-id="babf2-274">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="babf2-275">Il risultato potrebbero essere query senza parametri, quando invece è prevista la parametrizzazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-275">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-276">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-276">Mitigations</span></span>

<span data-ttu-id="babf2-277">Passare all'uso dei nuovi nomi di metodo.</span><span class="sxs-lookup"><span data-stu-id="babf2-277">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="babf2-278">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="babf2-278">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="babf2-279">Rilevamento del problema #15392</span><span class="sxs-lookup"><span data-stu-id="babf2-279">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-280">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-280">Old behavior</span></span>

<span data-ttu-id="babf2-281">Prima di EF Core 3,0, il metodo dati da tabelle ha tentato di rilevare se il SQL passato può essere composto in base a.</span><span class="sxs-lookup"><span data-stu-id="babf2-281">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="babf2-282">Ha fatto la valutazione del client quando SQL era non componibile come un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="babf2-282">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="babf2-283">La query seguente ha funzionato eseguendo il stored procedure sul server e FirstOrDefault sul lato client.</span><span class="sxs-lookup"><span data-stu-id="babf2-283">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="babf2-284">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-284">New behavior</span></span>

<span data-ttu-id="babf2-285">A partire da EF Core 3,0, EF Core non tenterà di analizzare SQL.</span><span class="sxs-lookup"><span data-stu-id="babf2-285">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="babf2-286">Quindi, se si esegue la composizione dopo FromSqlRaw/FromSqlInterpolated, EF Core comporrà il SQL causando una sottoquery.</span><span class="sxs-lookup"><span data-stu-id="babf2-286">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="babf2-287">Quindi, se si usa un stored procedure con Composition, si otterrà un'eccezione per la sintassi SQL non valida.</span><span class="sxs-lookup"><span data-stu-id="babf2-287">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-288">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-288">Why</span></span>

<span data-ttu-id="babf2-289">EF Core 3,0 non supporta la valutazione automatica del client perché è stata soggetta a errori, come illustrato [qui](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="babf2-289">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-290">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-290">Mitigations</span></span>

<span data-ttu-id="babf2-291">Se si usa un stored procedure in FromSqlRaw/FromSqlInterpolated, è noto che non è possibile componerlo, quindi è possibile aggiungere __AsEnumerable/AsAsyncEnumerable__ subito dopo la chiamata al metodo dati da tabelle per evitare qualsiasi composizione sul lato server.</span><span class="sxs-lookup"><span data-stu-id="babf2-291">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="babf2-292">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="babf2-292">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="babf2-293">Problema n. 15704</span><span class="sxs-lookup"><span data-stu-id="babf2-293">Tracking Issue #15704</span></span>](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-294">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-294">Old behavior</span></span>

<span data-ttu-id="babf2-295">Prima di EF Core 3.0, il metodo `FromSql` poteva essere specificato in un punto qualsiasi nella query.</span><span class="sxs-lookup"><span data-stu-id="babf2-295">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-296">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-296">New behavior</span></span>

<span data-ttu-id="babf2-297">A partire da EF Core 3.0, i nuovi metodi `FromSqlRaw` e `FromSqlInterpolated` (che sostituiscono`FromSql`) possono essere specificati solo per radici di query, ad esempio direttamente in `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="babf2-297">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="babf2-298">Qualsiasi tentativo di specificarli altrove causerà un errore di compilazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-298">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-299">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-299">Why</span></span>

<span data-ttu-id="babf2-300">La specifica di `FromSql` in qualsiasi posizione diversa da un `DbSet` non ha alcun significato aggiuntivo oppure valore aggiunto e può causare ambiguità in determinati scenari.</span><span class="sxs-lookup"><span data-stu-id="babf2-300">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-301">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-301">Mitigations</span></span>

<span data-ttu-id="babf2-302">Le chiamate di `FromSql` devono essere spostate in modo da comparire direttamente nel `DbSet` a cui si applicano.</span><span class="sxs-lookup"><span data-stu-id="babf2-302">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="babf2-303">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="babf2-303">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="babf2-304">Problema n. 13518</span><span class="sxs-lookup"><span data-stu-id="babf2-304">Tracking Issue #13518</span></span>](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-305">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-305">Old behavior</span></span>

<span data-ttu-id="babf2-306">Prima di EF Core 3.0, la stessa istanza di un'entità poteva essere usata per ogni occorrenza di un entità con tipo e ID specifici.</span><span class="sxs-lookup"><span data-stu-id="babf2-306">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="babf2-307">Questo comportamento corrisponde a quello delle query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="babf2-307">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="babf2-308">Ad esempio, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="babf2-308">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="babf2-309">restituisce la stessa istanza di `Category` per ogni `Product` associato alla categoria specificata.</span><span class="sxs-lookup"><span data-stu-id="babf2-309">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-310">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-310">New behavior</span></span>

<span data-ttu-id="babf2-311">A partire da EF Core 3.0, vengono create istanze di entità diverse quando un'entità con un determinato tipo e ID viene rilevata in posizioni diverse nel grafico restituito.</span><span class="sxs-lookup"><span data-stu-id="babf2-311">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="babf2-312">La query precedente, ad esempio, ora restituirà una nuova istanza di `Category` per ogni `Product` anche quando due prodotti sono associati alla stessa categoria.</span><span class="sxs-lookup"><span data-stu-id="babf2-312">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-313">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-313">Why</span></span>

<span data-ttu-id="babf2-314">La risoluzione delle identità (ovvero il processo per determinare che un'entità ha lo stesso tipo e ID di un'entità rilevata in precedenza) aggiunge un ulteriore sovraccarico della memoria e delle prestazioni,</span><span class="sxs-lookup"><span data-stu-id="babf2-314">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="babf2-315">il che va ad annullare il vantaggio derivante dall'uso delle query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="babf2-315">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="babf2-316">Inoltre, anche se in alcuni casi la risoluzione delle identità può essere utile, tuttavia non è necessaria se le entità devono essere serializzate e inviate a un client, come avviene comunemente con le query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="babf2-316">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-317">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-317">Mitigations</span></span>

<span data-ttu-id="babf2-318">Se la risoluzione delle identità è necessaria, usare una query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="babf2-318">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="babf2-319">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="babf2-319">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="babf2-320">Problema n. 12378</span><span class="sxs-lookup"><span data-stu-id="babf2-320">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-321">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-321">Old behavior</span></span>

<span data-ttu-id="babf2-322">Nelle versioni precedenti a EF Core 3.0 i valori temporanei venivano assegnati a tutte le proprietà di chiave per cui veniva in seguito generato un valore reale dal database.</span><span class="sxs-lookup"><span data-stu-id="babf2-322">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="babf2-323">In genere questi valori temporanei erano numeri negativi elevati.</span><span class="sxs-lookup"><span data-stu-id="babf2-323">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-324">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-324">New behavior</span></span>

<span data-ttu-id="babf2-325">A partire dalla versione 3.0, EF Core archivia il valore di chiave temporaneo come parte delle informazioni di rilevamento dell'entità e non modifica la proprietà di chiave.</span><span class="sxs-lookup"><span data-stu-id="babf2-325">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-326">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-326">Why</span></span>

<span data-ttu-id="babf2-327">Questa modifica è stata apportata per impedire che i valori di chiave temporanei diventino erroneamente permanenti quando un'entità rilevata in precedenza da un'istanza `DbContext` viene spostata in un'altra istanza `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="babf2-327">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-328">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-328">Mitigations</span></span>

<span data-ttu-id="babf2-329">Le applicazioni che assegnano valori di chiave primaria in chiavi esterne per creare associazioni tra le entità possono dipendere dal comportamento precedente se le chiavi primarie vengono generate dall'archivio e appartengono a entità con stato `Added`.</span><span class="sxs-lookup"><span data-stu-id="babf2-329">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="babf2-330">Questo può essere evitato:</span><span class="sxs-lookup"><span data-stu-id="babf2-330">This can be avoided by:</span></span>

* <span data-ttu-id="babf2-331">Non usando chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="babf2-331">Not using store-generated keys.</span></span>
* <span data-ttu-id="babf2-332">Impostando le proprietà di navigazione in modo da creare relazioni anziché impostando valori di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="babf2-332">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="babf2-333">Ottenendo i valori di chiave temporanei effettivi dalle informazioni di rilevamento dell'entità.</span><span class="sxs-lookup"><span data-stu-id="babf2-333">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="babf2-334">Ad esempio, `context.Entry(blog).Property(e => e.Id).CurrentValue` restituisce il valore temporaneo anche quando `blog.Id` non è stato impostato.</span><span class="sxs-lookup"><span data-stu-id="babf2-334">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="babf2-335">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="babf2-335">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="babf2-336">Problema n. 14616</span><span class="sxs-lookup"><span data-stu-id="babf2-336">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-337">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-337">Old behavior</span></span>

<span data-ttu-id="babf2-338">Nelle versioni precedenti a EF Core 3.0 un'entità non rilevata individuata da `DetectChanges` veniva rilevata nello stato `Added` e inserita come nuova riga quando veniva eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="babf2-338">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-339">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-339">New behavior</span></span>

<span data-ttu-id="babf2-340">A partire da EF Core 3.0, se un'entità usa valori di chiave generati e viene impostato un valore di chiave, l'entità viene rilevata nello stato `Modified`.</span><span class="sxs-lookup"><span data-stu-id="babf2-340">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="babf2-341">Ciò significa che si presuppone l'esistenza di una riga per l'entità che viene aggiornata quando viene eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="babf2-341">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="babf2-342">Se il valore di chiave non viene impostato o se il tipo di entità non usa chiavi generate, la nuova entità viene rilevata come `Added` come nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-342">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-343">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-343">Why</span></span>

<span data-ttu-id="babf2-344">Questa modifica è stata apportata per rendere più semplice e coerente l'uso di grafici di entità disconnesse con chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="babf2-344">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-345">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-345">Mitigations</span></span>

<span data-ttu-id="babf2-346">Questa modifica può interrompere un'applicazione se un tipo di entità è configurato per l'uso di chiavi generate ma i valori di chiave sono impostati in modo esplicito per le nuove istanze.</span><span class="sxs-lookup"><span data-stu-id="babf2-346">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="babf2-347">La correzione consiste nel configurare in modo esplicito le proprietà di chiave per non usare valori generati.</span><span class="sxs-lookup"><span data-stu-id="babf2-347">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="babf2-348">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="babf2-348">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="babf2-349">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="babf2-349">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="babf2-350">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-350">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="babf2-351">Problema n. 10114</span><span class="sxs-lookup"><span data-stu-id="babf2-351">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-352">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-352">Old behavior</span></span>

<span data-ttu-id="babf2-353">Nelle versioni precedenti alla versione 3.0, EF Core applicava azioni a catena (eliminazione delle entità dipendenti quando veniva eliminata un'entità di sicurezza obbligatoria o veniva recisa la relazione con un'entità di sicurezza obbligatoria) solo dopo la chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="babf2-353">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-354">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-354">New behavior</span></span>

<span data-ttu-id="babf2-355">A partire dalla versione 3.0, EF Core applica le azioni a catena non appena viene rilevata la condizione di attivazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-355">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="babf2-356">Ad esempio, la chiamata a `context.Remove()` per eliminare un'entità di sicurezza causa anche l'impostazione immediata di tutti i dipendenti obbligatori correlati rilevati su `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="babf2-356">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-357">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-357">Why</span></span>

<span data-ttu-id="babf2-358">Questa modifica è stata apportata per migliorare l'esperienza di associazione di dati e degli scenari di controllo in cui è importante individuare le entità che verranno eliminate _prima della chiamata a_ `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="babf2-358">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-359">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-359">Mitigations</span></span>

<span data-ttu-id="babf2-360">Il comportamento precedente può essere ripristinato tramite le impostazioni in `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="babf2-360">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="babf2-361">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-361">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="babf2-362">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="babf2-362">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="babf2-363">Rilevamento del problema #18022</span><span class="sxs-lookup"><span data-stu-id="babf2-363">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-364">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-364">Old behavior</span></span>

<span data-ttu-id="babf2-365">Prima del 3,0, il caricamento con entusiasmo degli spostamenti delle raccolte tramite `Include` gli operatori causava la generazione di più query nel database relazionale, una per ogni tipo di entità correlato.</span><span class="sxs-lookup"><span data-stu-id="babf2-365">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-366">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-366">New behavior</span></span>

<span data-ttu-id="babf2-367">A partire da 3,0, EF Core genera una singola query con JOIN nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="babf2-367">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-368">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-368">Why</span></span>

<span data-ttu-id="babf2-369">L'invio di più query per l'implementazione di una singola query LINQ ha causato numerosi problemi, incluse le prestazioni negative in quanto sono stati necessari più round trip di database e i dati coerenza problemi poiché ogni query poteva osservare uno stato diverso del database.</span><span class="sxs-lookup"><span data-stu-id="babf2-369">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-370">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-370">Mitigations</span></span>

<span data-ttu-id="babf2-371">Sebbene tecnicamente non si tratti di una modifica di rilievo, potrebbe avere un impatto significativo sulle prestazioni dell'applicazione quando una singola query contiene un numero elevato di operatori per le esplorazioni della `Include` raccolta.</span><span class="sxs-lookup"><span data-stu-id="babf2-371">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="babf2-372">Per ulteriori informazioni e per riscrivere le query in modo più efficiente, [vedere il commento](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="babf2-372">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="babf2-373">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="babf2-373">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="babf2-374">Problema n. 12661</span><span class="sxs-lookup"><span data-stu-id="babf2-374">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-375">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-375">Old behavior</span></span>

<span data-ttu-id="babf2-376">Prima della versione 3.0, `DeleteBehavior.Restrict` creava chiavi esterne nel database con la semantica `Restrict`, ma modificava anche la correzione interna in modo non ovvio.</span><span class="sxs-lookup"><span data-stu-id="babf2-376">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-377">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-377">New behavior</span></span>

<span data-ttu-id="babf2-378">A partire dalla versione 3.0, `DeleteBehavior.Restrict` assicura che le chiavi esterne vengano create con la semantica `Restrict`, ovvero non a cascata e con generazione di un'eccezione in caso di violazione di vincolo, senza influire sulla correzione interna di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="babf2-378">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-379">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-379">Why</span></span>

<span data-ttu-id="babf2-380">Questa modifica è stata apportata per migliorare l'esperienza di uso di `DeleteBehavior` in modo intuitivo, senza effetti collaterali imprevisti.</span><span class="sxs-lookup"><span data-stu-id="babf2-380">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-381">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-381">Mitigations</span></span>

<span data-ttu-id="babf2-382">Il comportamento precedente può essere ripristinato tramite `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="babf2-382">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="babf2-383">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="babf2-383">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="babf2-384">Problema n. 14194</span><span class="sxs-lookup"><span data-stu-id="babf2-384">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-385">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-385">Old behavior</span></span>

<span data-ttu-id="babf2-386">Nelle versioni precedenti a EF Core 3.0 i [tipi di query](xref:core/modeling/keyless-entity-types) erano uno strumento per eseguire query su dati che non definiscono una chiave primaria in modo strutturato.</span><span class="sxs-lookup"><span data-stu-id="babf2-386">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="babf2-387">Veniva infatti usato un tipo di query per eseguire il mapping di tipi di entità senza chiavi (più probabilmente da una vista, ma anche da una tabella), mentre veniva usato un tipo di entità normale quando era disponibile una chiave (più probabilmente da una tabella, ma anche da una vista).</span><span class="sxs-lookup"><span data-stu-id="babf2-387">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-388">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-388">New behavior</span></span>

<span data-ttu-id="babf2-389">Un tipo di query diventa ora semplicemente un tipo di entità senza chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="babf2-389">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="babf2-390">I tipi di entità senza chiave hanno la stessa funzionalità dei tipi di query nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-390">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-391">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-391">Why</span></span>

<span data-ttu-id="babf2-392">Questa modifica è stata apportata per ridurre la confusione riguardo lo scopo dei tipi di query.</span><span class="sxs-lookup"><span data-stu-id="babf2-392">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="babf2-393">In particolare, si tratta di tipi di entità senza chiave che sono intrinsecamente di sola lettura per questo motivo ma non dovrebbero essere usati solo perché un tipo di entità deve essere di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="babf2-393">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="babf2-394">Analogamente, spesso vengono mappati alle viste solo perché le viste spesso non definiscono le chiavi.</span><span class="sxs-lookup"><span data-stu-id="babf2-394">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-395">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-395">Mitigations</span></span>

<span data-ttu-id="babf2-396">Le parti dell'API seguenti sono ora obsolete:</span><span class="sxs-lookup"><span data-stu-id="babf2-396">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="babf2-397">**`ModelBuilder.Query<>()`** -È invece necessario `ModelBuilder.Entity<>().HasNoKey()` chiamare il metodo per contrassegnare un tipo di entità senza chiavi.</span><span class="sxs-lookup"><span data-stu-id="babf2-397">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="babf2-398">Non ne viene eseguita la configurazione per convenzione per evitare una configurazione errata quando è prevista una chiave primaria che tuttavia non corrisponde alla convenzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-398">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="babf2-399">**`DbQuery<>`** - `DbSet<>` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="babf2-399">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="babf2-400">**`DbContext.Query<>()`** - `DbContext.Set<>()` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="babf2-400">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="babf2-401">**`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="babf2-401">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="babf2-402">A causa di [un problema in 3. x](https://github.com/dotnet/efcore/issues/19537) durante l'esecuzione di query su entità senza chiave che dispongono di tutte le proprietà impostate su `null` un `null` verrà restituito al posto di un'entità, se il problema è applicabile anche allo scenario, aggiungere la logica per gestire `null` i risultati.</span><span class="sxs-lookup"><span data-stu-id="babf2-402">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="babf2-403">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="babf2-403">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="babf2-404">[Rilevamento del problema #12444](https://github.com/dotnet/efcore/issues/12444) 
 [Rilevamento del problema #9148](https://github.com/dotnet/efcore/issues/9148) 
 [Rilevamento del problema #14153](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="babf2-404">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="babf2-405">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-405">Old behavior</span></span>

<span data-ttu-id="babf2-406">Nelle versioni precedenti a EF Core 3.0 la configurazione della relazione di proprietà veniva eseguita direttamente dopo la chiamata a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="babf2-406">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-407">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-407">New behavior</span></span>

<span data-ttu-id="babf2-408">A partire da EF Core 3.0, è disponibile l'API Fluent per configurare una proprietà di navigazione per il proprietario usando `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="babf2-408">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="babf2-409">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-409">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="babf2-410">La configurazione correlata alla relazione tra proprietario e elemento di proprietà deve essere ora concatenata dopo `WithOwner()` in modo analogo a come vengono configurate altre relazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-410">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="babf2-411">La configurazione per il tipo di proprietà viene comunque concatenata dopo `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="babf2-411">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="babf2-412">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-412">For example:</span></span>

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

<span data-ttu-id="babf2-413">Inoltre, la chiamata a `Entity()`, `HasOne()` o `Set()` con una destinazione di tipo di proprietà genera ora un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="babf2-413">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-414">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-414">Why</span></span>

<span data-ttu-id="babf2-415">Questa modifica è stata apportata per creare una separazione più netta tra la configurazione del tipo di proprietà e la _relazione_ con il tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-415">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="babf2-416">Ciò consente di eliminare ambiguità e confusione su metodi come `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="babf2-416">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-417">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-417">Mitigations</span></span>

<span data-ttu-id="babf2-418">Modificare la configurazione delle relazioni dei tipi di proprietà per usare la superficie della nuova API come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="babf2-418">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="babf2-419">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="babf2-419">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="babf2-420">Problema n. 9005</span><span class="sxs-lookup"><span data-stu-id="babf2-420">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-421">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-421">Old behavior</span></span>

<span data-ttu-id="babf2-422">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="babf2-422">Consider the following model:</span></span>

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

<span data-ttu-id="babf2-423">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o viene mappato in modo esplicito alla stessa tabella, era sempre necessaria un'istanza di `OrderDetails` per l'aggiunta di un nuovo `Order`.</span><span class="sxs-lookup"><span data-stu-id="babf2-423">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-424">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-424">New behavior</span></span>

<span data-ttu-id="babf2-425">A partire dalla versione 3.0, EF Core consente di aggiungere un `Order` senza un `OrderDetails` ed esegue il mapping di tutte le proprietà di `OrderDetails`, tranne che la chiave primaria, a colonne che ammettono valori Null.</span><span class="sxs-lookup"><span data-stu-id="babf2-425">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="babf2-426">In fase di query, EF Core imposta `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.</span><span class="sxs-lookup"><span data-stu-id="babf2-426">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-427">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-427">Mitigations</span></span>

<span data-ttu-id="babf2-428">Se il modello include una tabella condivisa dipendente con tutte le colonne facoltative, ma è previsto che la navigazione che punta a essa non sia `null`, l'applicazione deve essere modificata per gestire casi in cui la navigazione è `null`.</span><span class="sxs-lookup"><span data-stu-id="babf2-428">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="babf2-429">Se questo non è possibile, è consigliabile aggiungere una proprietà obbligatoria al tipo di entità o assegnare un valore non `null` ad almeno una proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-429">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="babf2-430">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="babf2-430">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="babf2-431">Problema n. 14154</span><span class="sxs-lookup"><span data-stu-id="babf2-431">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-432">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-432">Old behavior</span></span>

<span data-ttu-id="babf2-433">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="babf2-433">Consider the following model:</span></span>

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

<span data-ttu-id="babf2-434">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o è mappato in modo esplicito alla stessa tabella, il solo aggiornamento di `OrderDetails` non aggiornerà il valore `Version` nel client e l'aggiornamento successivo avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="babf2-434">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-435">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-435">New behavior</span></span>

<span data-ttu-id="babf2-436">A partire dalla versione 3.0, EF Core propaga il nuovo valore `Version` a `Order` se è proprietario di `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="babf2-436">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="babf2-437">In caso contrario, viene generata un'eccezione durante la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="babf2-437">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-438">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-438">Why</span></span>

<span data-ttu-id="babf2-439">Questa modifica è stata apportata per evitare un valore del token di concorrenza non aggiornato quando viene aggiornata solo una delle entità mappate alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="babf2-439">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-440">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-440">Mitigations</span></span>

<span data-ttu-id="babf2-441">Tutte le entità che condividono la tabella devono includere una proprietà mappata alla colonna del token di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-441">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="babf2-442">È possibile crearne una in stato shadow:</span><span class="sxs-lookup"><span data-stu-id="babf2-442">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="babf2-443">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="babf2-443">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="babf2-444">Rilevamento del problema #18876</span><span class="sxs-lookup"><span data-stu-id="babf2-444">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-445">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-445">Old behavior</span></span>

<span data-ttu-id="babf2-446">Prima di EF Core 3,0, le entità di proprietà potevano essere sottoposte a query come qualsiasi altra navigazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-446">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="babf2-447">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-447">New behavior</span></span>

<span data-ttu-id="babf2-448">A partire da 3,0, EF Core genererà un'operazione se una query di rilevamento proietta un'entità di proprietà senza proprietario.</span><span class="sxs-lookup"><span data-stu-id="babf2-448">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-449">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-449">Why</span></span>

<span data-ttu-id="babf2-450">Le entità di proprietà non possono essere modificate senza il proprietario, quindi nella maggior parte dei casi l'esecuzione di query in questo modo è un errore.</span><span class="sxs-lookup"><span data-stu-id="babf2-450">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-451">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-451">Mitigations</span></span>

<span data-ttu-id="babf2-452">Se l'entità di proprietà deve essere rilevata in modo da essere modificata in un secondo momento, il proprietario deve essere incluso nella query.</span><span class="sxs-lookup"><span data-stu-id="babf2-452">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="babf2-453">In caso contrario `AsNoTracking()` , aggiungere una chiamata:</span><span class="sxs-lookup"><span data-stu-id="babf2-453">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="babf2-454">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="babf2-454">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="babf2-455">Problema n. 13998</span><span class="sxs-lookup"><span data-stu-id="babf2-455">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-456">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-456">Old behavior</span></span>

<span data-ttu-id="babf2-457">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="babf2-457">Consider the following model:</span></span>

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

<span data-ttu-id="babf2-458">Prima di EF Core 3.0, per la proprietà `ShippingAddress` sarebbe stato eseguito il mapping a colonne separate per `BulkOrder` e `Order` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="babf2-458">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-459">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-459">New behavior</span></span>

<span data-ttu-id="babf2-460">A partire dalla versione 3.0, EF Core crea solo una colonna per `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="babf2-460">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-461">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-461">Why</span></span>

<span data-ttu-id="babf2-462">Il comportamento precedente non era previsto.</span><span class="sxs-lookup"><span data-stu-id="babf2-462">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-463">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-463">Mitigations</span></span>

<span data-ttu-id="babf2-464">È ancora possibile eseguire il mapping esplicito della proprietà a una colonna separata per i tipi derivati:</span><span class="sxs-lookup"><span data-stu-id="babf2-464">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="babf2-465">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="babf2-465">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="babf2-466">Problema n. 13274</span><span class="sxs-lookup"><span data-stu-id="babf2-466">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-467">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-467">Old behavior</span></span>

<span data-ttu-id="babf2-468">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="babf2-468">Consider the following model:</span></span>

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

<span data-ttu-id="babf2-469">Nelle versioni precedenti a EF Core 3.0 veniva usata la proprietà `CustomerId` per la chiave esterna per convenzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-469">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="babf2-470">Tuttavia, se `Order` è un tipo di proprietà, `CustomerId` sarebbe la chiave primaria e ciò non è in genere auspicabile.</span><span class="sxs-lookup"><span data-stu-id="babf2-470">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-471">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-471">New behavior</span></span>

<span data-ttu-id="babf2-472">A partire da 3.0, EF Core non tenta di usare le proprietà per le chiavi esterne per convenzione se hanno lo stesso nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="babf2-472">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="babf2-473">Viene ancora eseguita la corrispondenza tra i criteri del nome del tipo dell'entità di sicurezza concatenato al nome della proprietà dell'entità di sicurezza e il nome di navigazione concatenato al nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="babf2-473">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="babf2-474">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-474">For example:</span></span>

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

#### <a name="why"></a><span data-ttu-id="babf2-475">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-475">Why</span></span>

<span data-ttu-id="babf2-476">Questa modifica è stata apportata per evitare una definizione errata della proprietà di chiave primaria nel tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-476">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-477">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-477">Mitigations</span></span>

<span data-ttu-id="babf2-478">Se la proprietà è stata progettata per essere la chiave esterna e di conseguenza parte della chiave primaria, configurarla in modo esplicito come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="babf2-478">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="babf2-479">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="babf2-479">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="babf2-480">Problema n. 14218</span><span class="sxs-lookup"><span data-stu-id="babf2-480">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-481">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-481">Old behavior</span></span>

<span data-ttu-id="babf2-482">Prima di EF Core 3.0, se il contesto apre la connessione all'interno di un `TransactionScope`, la connessione rimane aperta mentre è attivo il `TransactionScope` corrente.</span><span class="sxs-lookup"><span data-stu-id="babf2-482">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="babf2-483">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-483">New behavior</span></span>

<span data-ttu-id="babf2-484">A partire dalla versione 3.0, EF Core chiude la connessione non appena non viene più usata.</span><span class="sxs-lookup"><span data-stu-id="babf2-484">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-485">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-485">Why</span></span>

<span data-ttu-id="babf2-486">Questa modifica consente di usare più contesti nello stesso `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="babf2-486">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="babf2-487">Il nuovo comportamento corrisponde anche a EF6.</span><span class="sxs-lookup"><span data-stu-id="babf2-487">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-488">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-488">Mitigations</span></span>

<span data-ttu-id="babf2-489">Se la connessione deve rimanere aperta, la chiamata esplicita di `OpenConnection()` garantirà che EF Core non la chiuda prematuramente:</span><span class="sxs-lookup"><span data-stu-id="babf2-489">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="babf2-490">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="babf2-490">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="babf2-491">Problema n. 6872</span><span class="sxs-lookup"><span data-stu-id="babf2-491">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-492">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-492">Old behavior</span></span>

<span data-ttu-id="babf2-493">Nelle versioni precedenti a EF Core 3.0 veniva usato un unico generatore di valori condiviso per tutte le proprietà di chiavi di tipo intero in memoria.</span><span class="sxs-lookup"><span data-stu-id="babf2-493">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-494">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-494">New behavior</span></span>

<span data-ttu-id="babf2-495">A partire da EF Core 3.0, ogni proprietà di chiave di tipo intero riceve un generatore di valori quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="babf2-495">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="babf2-496">Inoltre, se il database viene eliminato, la generazione di chiavi viene reimpostata per tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="babf2-496">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-497">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-497">Why</span></span>

<span data-ttu-id="babf2-498">Questa modifica è stata apportata per allineare maggiormente la generazione di chiavi in memoria alla generazione di chiavi del database reale e per migliorare la possibilità di isolare i test l'uno dall'altro quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="babf2-498">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-499">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-499">Mitigations</span></span>

<span data-ttu-id="babf2-500">Ciò può interrompere un'applicazione che si basa sull'impostazione di valori di chiave in memoria specifici.</span><span class="sxs-lookup"><span data-stu-id="babf2-500">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="babf2-501">È consigliabile non basare l'applicazione su valori di chiave specifici o eseguire l'aggiornamento per passare al nuovo comportamento.</span><span class="sxs-lookup"><span data-stu-id="babf2-501">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="babf2-502">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-502">Backing fields are used by default</span></span>

[<span data-ttu-id="babf2-503">Problema n. 12430</span><span class="sxs-lookup"><span data-stu-id="babf2-503">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-504">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-504">Old behavior</span></span>

<span data-ttu-id="babf2-505">Nelle versioni precedenti alla versione 3.0, anche se il campo sottostante di una proprietà era noto, per impostazione predefinita EF Core eseguiva la lettura e la scrittura del valore della proprietà usando i metodi getter e setter della proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-505">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="babf2-506">L'eccezione era costituita dall'esecuzione di query in cui il campo sottostante, se noto, veniva impostato direttamente.</span><span class="sxs-lookup"><span data-stu-id="babf2-506">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-507">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-507">New behavior</span></span>

<span data-ttu-id="babf2-508">A partire da EF Core 3.0, se il campo sottostante di una proprietà è noto, la lettura e la scrittura della proprietà vengono sempre eseguite usando il campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="babf2-508">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="babf2-509">Ciò potrebbe causare un'interruzione dell'applicazione se l'applicazione si basa su un comportamento aggiuntivo codificato nei metodi getter o setter.</span><span class="sxs-lookup"><span data-stu-id="babf2-509">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-510">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-510">Why</span></span>

<span data-ttu-id="babf2-511">Questa modifica è stata apportata per impedire a EF Core di attivare per errore la logica di business per impostazione predefinita quando si eseguono operazioni di database che interessano le entità.</span><span class="sxs-lookup"><span data-stu-id="babf2-511">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-512">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-512">Mitigations</span></span>

<span data-ttu-id="babf2-513">È possibile ripristinare il comportamento delle versioni precedenti alla versione 3.0 tramite la configurazione della modalità di accesso delle proprietà in `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="babf2-513">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="babf2-514">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-514">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="babf2-515">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="babf2-515">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="babf2-516">Problema n. 12523</span><span class="sxs-lookup"><span data-stu-id="babf2-516">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-517">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-517">Old behavior</span></span>

<span data-ttu-id="babf2-518">Nelle versioni precedenti a EF Core 3.0, se più campi soddisfacevano le regole di ricerca del campo sottostante di una proprietà, veniva selezionato un solo campo in base a un ordine di precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-518">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="babf2-519">Ciò poteva causare l'uso di un campo non corretto nei casi ambigui.</span><span class="sxs-lookup"><span data-stu-id="babf2-519">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-520">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-520">New behavior</span></span>

<span data-ttu-id="babf2-521">A partire da EF Core 3.0, se più campi corrispondono alla stessa proprietà, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="babf2-521">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-522">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-522">Why</span></span>

<span data-ttu-id="babf2-523">Questa modifica è stata apportata per evitare di usare automaticamente un campo rispetto a un altro quando un solo campo può essere quello corretto.</span><span class="sxs-lookup"><span data-stu-id="babf2-523">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-524">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-524">Mitigations</span></span>

<span data-ttu-id="babf2-525">Per le proprietà con campi sottostanti ambigui, il campo da usare deve essere specificato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="babf2-525">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="babf2-526">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="babf2-526">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="babf2-527">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="babf2-527">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="babf2-528">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-528">Old behavior</span></span>

<span data-ttu-id="babf2-529">Prima di EF Core 3,0, una proprietà può essere specificata da un valore stringa e, se non è stata trovata alcuna proprietà con tale nome nel tipo .NET, EF Core tenterà di associarla a un campo usando le regole di convenzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-529">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="babf2-530">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-530">New behavior</span></span>

<span data-ttu-id="babf2-531">A partire da EF Core 3.0, una proprietà solo campo deve corrispondere esattamente al nome del campo.</span><span class="sxs-lookup"><span data-stu-id="babf2-531">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="babf2-532">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-532">Why</span></span>

<span data-ttu-id="babf2-533">Questa modifica è stata introdotta per evitare di usare lo stesso campo per due proprietà con nome simile. Le regole di corrispondenza per le proprietà solo campo sono state anche uniformate a quelle per le proprietà mappate a proprietà CLR.</span><span class="sxs-lookup"><span data-stu-id="babf2-533">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-534">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-534">Mitigations</span></span>

<span data-ttu-id="babf2-535">Le proprietà solo campo devono avere lo stesso nome del campo a cui vengono mappate.</span><span class="sxs-lookup"><span data-stu-id="babf2-535">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="babf2-536">In una versione futura di EF Core dopo il 3,0, si prevede di abilitare di nuovo in modo esplicito il nome di un campo diverso dal nome della proprietà (vedere il problema [#15307](https://github.com/dotnet/efcore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="babf2-536">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="babf2-537">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="babf2-537">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="babf2-538">Problema n. 14756</span><span class="sxs-lookup"><span data-stu-id="babf2-538">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-539">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-539">Old behavior</span></span>

<span data-ttu-id="babf2-540">Prima di EF Core 3,0, chiamando `AddDbContext` o `AddDbContextPool` sarebbero stati registrati anche i servizi di memorizzazione nella cache e di registrazione della memoria con le chiamate a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) e [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="babf2-540">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-541">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-541">New behavior</span></span>

<span data-ttu-id="babf2-542">A partire da EF Core 3.0, `AddDbContext` e `AddDbContextPool` non registreranno più questi servizi con inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="babf2-542">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-543">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-543">Why</span></span>

<span data-ttu-id="babf2-544">EF Core 3.0 non richiede che questi servizi siano inclusi nel contenitore di inserimento delle dipendenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-544">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="babf2-545">Tuttavia, se `ILoggerFactory` è registrato nel contenitore di inserimento delle dipendenze dell'applicazione, verrà ancora usato da EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-545">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-546">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-546">Mitigations</span></span>

<span data-ttu-id="babf2-547">Se l'applicazione necessita di questi servizi, registrarli in modo esplicito con il contenitore di inserimento delle dipendenze usando [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="babf2-547">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="babf2-548">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="babf2-548">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="babf2-549">Rilevamento del problema #12905</span><span class="sxs-lookup"><span data-stu-id="babf2-549">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-550">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-550">Old behavior</span></span>

<span data-ttu-id="babf2-551">Prima di EF Core 3,0, i metodi di chiamata `AddEntityFramework*` registravano anche i servizi di memorizzazione nella cache di memoria con di senza un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-551">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-552">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-552">New behavior</span></span>

<span data-ttu-id="babf2-553">A partire da EF Core 3,0, registrerà `AddEntityFramework*` un servizio IMemoryCache con un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-553">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="babf2-554">Se altri servizi aggiunti successivamente dipendono da IMemoryCache, è possibile raggiungere rapidamente il limite predefinito che causa eccezioni o prestazioni ridotte.</span><span class="sxs-lookup"><span data-stu-id="babf2-554">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-555">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-555">Why</span></span>

<span data-ttu-id="babf2-556">L'utilizzo di IMemoryCache senza un limite può comportare l'utilizzo di memoria non controllata se è presente un bug nella logica di memorizzazione nella cache delle query o se le query vengono generate in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="babf2-556">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="babf2-557">La presenza di un limite predefinito attenua un potenziale attacco DoS.</span><span class="sxs-lookup"><span data-stu-id="babf2-557">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-558">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-558">Mitigations</span></span>

<span data-ttu-id="babf2-559">Nella maggior parte dei casi, la chiamata a `AddEntityFramework*` non è necessaria se `AddDbContext` `AddDbContextPool` viene chiamato anche o.</span><span class="sxs-lookup"><span data-stu-id="babf2-559">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="babf2-560">Pertanto, la migliore mitigazione consiste nel rimuovere la `AddEntityFramework*` chiamata.</span><span class="sxs-lookup"><span data-stu-id="babf2-560">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="babf2-561">Se l'applicazione necessita di questi servizi, registrare un'implementazione di IMemoryCache in modo esplicito con il contenitore DI inserimento in anticipo usando [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="babf2-561">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="babf2-562">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="babf2-562">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="babf2-563">Problema n. 13552</span><span class="sxs-lookup"><span data-stu-id="babf2-563">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-564">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-564">Old behavior</span></span>

<span data-ttu-id="babf2-565">Nelle versioni precedenti a EF Core 3.0 la chiamata a `DbContext.Entry` causava il rilevamento delle modifiche per tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="babf2-565">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="babf2-566">Ciò garantiva l'aggiornamento dello stato esposto in `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="babf2-566">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-567">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-567">New behavior</span></span>

<span data-ttu-id="babf2-568">A partire da EF Core 3.0, la chiamata a `DbContext.Entry` causa ora solo il tentativo di rilevare le modifiche nell'entità specificata e in tutte le relative entità di sicurezza rilevate.</span><span class="sxs-lookup"><span data-stu-id="babf2-568">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="babf2-569">Ciò significa che le modifiche apportate altrove potrebbero non essere state rilevate tramite la chiamata al metodo e ciò potrebbe avere implicazioni sullo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-569">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="babf2-570">Si noti che se `ChangeTracker.AutoDetectChangesEnabled` è impostato su `false`, verrà disabilitato anche questo tipo di rilevamento delle modifiche locali.</span><span class="sxs-lookup"><span data-stu-id="babf2-570">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="babf2-571">Gli altri metodi che causano il rilevamento delle modifiche, ad esempio `ChangeTracker.Entries` e `SaveChanges`, causano ancora un `DetectChanges` completo di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="babf2-571">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-572">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-572">Why</span></span>

<span data-ttu-id="babf2-573">Questa modifica è stata apportata per migliorare le prestazioni predefinite dell'uso di `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="babf2-573">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-574">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-574">Mitigations</span></span>

<span data-ttu-id="babf2-575">Chiamare `ChangeTracker.DetectChanges()` in modo esplicito prima di chiamare `Entry` per garantire il comportamento precedente alla versione 3.0.</span><span class="sxs-lookup"><span data-stu-id="babf2-575">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="babf2-576">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-576">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="babf2-577">Problema n. 14617</span><span class="sxs-lookup"><span data-stu-id="babf2-577">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-578">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-578">Old behavior</span></span>

<span data-ttu-id="babf2-579">Nelle versioni precedenti a EF Core 3.0 le proprietà di chiave `string` e `byte[]` potevano essere usate senza impostare in modo esplicito un valore non Null.</span><span class="sxs-lookup"><span data-stu-id="babf2-579">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="babf2-580">In questi casi, il valore di chiave veniva generato nel client come GUID, serializzato in byte per `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="babf2-580">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-581">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-581">New behavior</span></span>

<span data-ttu-id="babf2-582">A partire da EF Core 3.0 viene generata un'eccezione che indica che non è stato impostato alcun valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="babf2-582">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-583">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-583">Why</span></span>

<span data-ttu-id="babf2-584">Questa modifica è stata apportata poiché i valori `string`/`byte[]` generati dal client non risultano in genere utili e il comportamento predefinito rendeva complesso ragionare sui valori di chiave generati in un modo comune.</span><span class="sxs-lookup"><span data-stu-id="babf2-584">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-585">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-585">Mitigations</span></span>

<span data-ttu-id="babf2-586">È possibile ripristinare il comportamento precedente alla versione 3.0 specificando in modo esplicito che le proprietà di chiave devono usare i valori generati se non viene impostato alcun altro valore non Null.</span><span class="sxs-lookup"><span data-stu-id="babf2-586">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="babf2-587">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="babf2-587">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="babf2-588">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="babf2-588">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="babf2-589">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="babf2-589">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="babf2-590">Problema n. 14698</span><span class="sxs-lookup"><span data-stu-id="babf2-590">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-591">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-591">Old behavior</span></span>

<span data-ttu-id="babf2-592">Nelle versioni precedenti a EF Core 3.0 `ILoggerFactory` veniva registrato come servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="babf2-592">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-593">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-593">New behavior</span></span>

<span data-ttu-id="babf2-594">A partire da EF Core 3.0, `ILoggerFactory` viene registrato come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="babf2-594">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-595">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-595">Why</span></span>

<span data-ttu-id="babf2-596">Questa modifica è stata apportata per consentire l'associazione di un logger a un'istanza `DbContext` che abilita altre funzionalità e rimuove alcuni casi di comportamento anomalo, ad esempio un'esplosione dei provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="babf2-596">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-597">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-597">Mitigations</span></span>

<span data-ttu-id="babf2-598">Questa modifica non dovrebbe influire sul codice dell'applicazione a meno che non vengano registrati e usati servizi personalizzati nel provider di servizi interno di EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-598">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="babf2-599">Questo non è un comportamento comune.</span><span class="sxs-lookup"><span data-stu-id="babf2-599">This isn't common.</span></span>
<span data-ttu-id="babf2-600">In questi casi la maggior parte delle operazioni continuano a essere eseguite correttamente, ma qualsiasi servizio singleton dipendente da `ILoggerFactory` dovrà essere modificato per ottenere `ILoggerFactory` in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="babf2-600">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="babf2-601">Se si verificano situazioni simili, inviare una segnalazione nello [strumento di gestione dei problemi in GitHub relativo a EF Core](https://github.com/dotnet/efcore/issues) per comunicare in che modo viene usato `ILoggerFactory` per consentirci di comprendere meglio come evitare ulteriori interruzioni in futuro.</span><span class="sxs-lookup"><span data-stu-id="babf2-601">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="babf2-602">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="babf2-602">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="babf2-603">Problema n. 12780</span><span class="sxs-lookup"><span data-stu-id="babf2-603">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-604">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-604">Old behavior</span></span>

<span data-ttu-id="babf2-605">Nelle versioni precedenti a EF Core 3.0, quando `DbContext` veniva eliminato non esisteva alcun metodo per scoprire se una determinata proprietà di navigazione in un'entità ottenuta da un contesto specifico veniva caricata completamente o meno.</span><span class="sxs-lookup"><span data-stu-id="babf2-605">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="babf2-606">I proxy presupponevano invece che venisse caricata una navigazione di riferimento se era presente un valore non Null e che venisse caricata una navigazione di raccolta se era presente un valore.</span><span class="sxs-lookup"><span data-stu-id="babf2-606">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="babf2-607">In questi casi il tentativo di eseguire un caricamento lazy non avrebbe avuto alcun esito.</span><span class="sxs-lookup"><span data-stu-id="babf2-607">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-608">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-608">New behavior</span></span>

<span data-ttu-id="babf2-609">A partire da Entity Framework Core 3.0, i proxy tengono traccia del caricamento o mancato caricamento di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-609">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="babf2-610">Ciò significa che il tentativo di accedere a una proprietà di navigazione caricata dopo l'eliminazione del contesto non avrà mai alcun esito, anche quando la navigazione caricata è vuota o ha valore Null.</span><span class="sxs-lookup"><span data-stu-id="babf2-610">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="babf2-611">Al contrario, il tentativo di accedere a una proprietà di navigazione non caricata genera un'eccezione se il contesto viene eliminato anche se la proprietà di navigazione è una raccolta non vuota.</span><span class="sxs-lookup"><span data-stu-id="babf2-611">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="babf2-612">Se si verifica questa situazione significa che il codice dell'applicazione sta tentando di usare il caricamento lazy in un momento non valido e l'applicazione deve essere modificata in modo da non eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-612">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-613">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-613">Why</span></span>

<span data-ttu-id="babf2-614">Questa modifica è stata apportata per rendere coerente e corretto il comportamento durante un tentativo di caricamento lazy in un'istanza `DbContext` eliminata.</span><span class="sxs-lookup"><span data-stu-id="babf2-614">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-615">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-615">Mitigations</span></span>

<span data-ttu-id="babf2-616">Aggiornare il codice dell'applicazione per fare in modo che non venga tentato il caricamento lazy con un contesto eliminato oppure specificare una configurazione in modo che non venga eseguita alcuna operazione come descritto nel messaggio di eccezione.</span><span class="sxs-lookup"><span data-stu-id="babf2-616">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="babf2-617">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="babf2-617">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="babf2-618">Problema n. 10236</span><span class="sxs-lookup"><span data-stu-id="babf2-618">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-619">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-619">Old behavior</span></span>

<span data-ttu-id="babf2-620">Nelle versioni precedenti a EF Core 3.0 veniva registrato un avviso per le applicazioni che creavano un numero eccessivo di provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="babf2-620">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-621">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-621">New behavior</span></span>

<span data-ttu-id="babf2-622">A partire da EF Core 3.0, l'avviso viene considerato un errore e viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="babf2-622">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-623">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-623">Why</span></span>

<span data-ttu-id="babf2-624">Questa modifica è stata apportata per gestire meglio il codice dell'applicazione tramite un'esposizione più esplicita di questa situazione di errore.</span><span class="sxs-lookup"><span data-stu-id="babf2-624">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-625">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-625">Mitigations</span></span>

<span data-ttu-id="babf2-626">L'azione più appropriata quando si verifica questo errore consiste nell'individuare la causa radice e nell'interrompere la creazione di numerosi provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="babf2-626">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="babf2-627">È possibile tuttavia convertire nuovamente l'errore in avviso o ignorarlo tramite una configurazione in `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="babf2-627">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="babf2-628">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-628">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="babf2-629">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="babf2-629">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="babf2-630">Problema n. 9171</span><span class="sxs-lookup"><span data-stu-id="babf2-630">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-631">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-631">Old behavior</span></span>

<span data-ttu-id="babf2-632">Prima di EF Core 3.0, il codice che chiama `HasOne` o `HasMany` con una singola stringa era interpretato in modo poco chiaro.</span><span class="sxs-lookup"><span data-stu-id="babf2-632">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="babf2-633">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-633">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="babf2-634">Apparentemente, il codice mette in relazione `Samurai` con un altro tipo di entità tramite la proprietà di navigazione `Entrance`, che può essere privata.</span><span class="sxs-lookup"><span data-stu-id="babf2-634">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="babf2-635">In realtà, il codice tenta di creare una relazione con un tipo di entità denominato `Entrance` senza proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-635">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-636">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-636">New behavior</span></span>

<span data-ttu-id="babf2-637">A partire da EF Core 3.0, il codice sopra riportato ora esegue quello che avrebbe dovuto fare in precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-637">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-638">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-638">Why</span></span>

<span data-ttu-id="babf2-639">Il comportamento precedente era molto poco chiaro, soprattutto durante la lettura del codice di configurazione e la ricerca di errori.</span><span class="sxs-lookup"><span data-stu-id="babf2-639">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-640">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-640">Mitigations</span></span>

<span data-ttu-id="babf2-641">Questa modifica causerà problemi solo nelle applicazioni che configurano relazioni in modo esplicito usando stringhe per i nomi dei tipi e senza specificare in modo esplicito la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-641">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="babf2-642">Non è uno scenario comune.</span><span class="sxs-lookup"><span data-stu-id="babf2-642">This is not common.</span></span>
<span data-ttu-id="babf2-643">Il comportamento precedente può essere ottenuto passando esplicitamente `null` per il nome della proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-643">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="babf2-644">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-644">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="babf2-645">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="babf2-645">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="babf2-646">Problema n. 15184</span><span class="sxs-lookup"><span data-stu-id="babf2-646">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-647">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-647">Old behavior</span></span>

<span data-ttu-id="babf2-648">I metodi asincroni seguenti in precedenza restituivano il tipo `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="babf2-648">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="babf2-649">`ValueGenerator.NextValueAsync()` (e classi derivate)</span><span class="sxs-lookup"><span data-stu-id="babf2-649">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-650">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-650">New behavior</span></span>

<span data-ttu-id="babf2-651">I metodi indicati in precedenza ora restituiscono il tipo `ValueTask<T>` sullo stesso `T` come in precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-651">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-652">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-652">Why</span></span>

<span data-ttu-id="babf2-653">Questa modifica riduce il numero delle allocazioni di heap sostenute quando si richiamano questi metodi, con un miglioramento generale delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-653">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-654">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-654">Mitigations</span></span>

<span data-ttu-id="babf2-655">Le applicazioni semplicemente in attesa delle API precedenti devono solo essere ricompilate e non sono richieste modifiche del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="babf2-655">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="babf2-656">Per scenari di utilizzo più complessi (ad esempio, il passaggio del tipo `Task` restituito a `Task.WhenAny()`) è richiesto in genere che il tipo `ValueTask<T>` restituito venga convertito in `Task<T>` chiamando `AsTask()` su di esso.</span><span class="sxs-lookup"><span data-stu-id="babf2-656">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="babf2-657">Si noti che in questo modo si annulla la riduzione delle allocazioni consentita da questa modifica.</span><span class="sxs-lookup"><span data-stu-id="babf2-657">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="babf2-658">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="babf2-658">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="babf2-659">Problema n. 9913</span><span class="sxs-lookup"><span data-stu-id="babf2-659">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-660">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-660">Old behavior</span></span>

<span data-ttu-id="babf2-661">Il nome di annotazione delle annotazioni di mapping del tipo era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="babf2-661">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-662">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-662">New behavior</span></span>

<span data-ttu-id="babf2-663">Il nome di annotazione delle annotazioni di mapping del tipo è ora "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="babf2-663">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-664">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-664">Why</span></span>

<span data-ttu-id="babf2-665">Il mapping dei tipi non viene più usato solo per i provider di database relazionali.</span><span class="sxs-lookup"><span data-stu-id="babf2-665">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-666">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-666">Mitigations</span></span>

<span data-ttu-id="babf2-667">Ciò causa un'interruzione solo nelle applicazioni che accedono al mapping dei tipi direttamente come annotazione. Questa situazione non è comune.</span><span class="sxs-lookup"><span data-stu-id="babf2-667">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="babf2-668">L'azione più appropriata per risolvere il problema consiste nell'usare la superficie dell'API per accedere al mapping dei tipi anziché l'annotazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-668">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="babf2-669">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="babf2-669">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="babf2-670">Problema n. 11811</span><span class="sxs-lookup"><span data-stu-id="babf2-670">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-671">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-671">Old behavior</span></span>

<span data-ttu-id="babf2-672">Nelle versioni precedenti a EF Core 3.0, la chiamata a `ToTable()` in un tipo derivato veniva ignorata poiché soltanto la strategia di mapping dell'ereditarietà era una tabella per gerarchia dove la chiamata non era valida.</span><span class="sxs-lookup"><span data-stu-id="babf2-672">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-673">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-673">New behavior</span></span>

<span data-ttu-id="babf2-674">A partire da EF Core 3.0 e in preparazione all'aggiunta del supporto per la tabella per tipo e per TPC in una versione successiva, la chiamata a `ToTable()` in un tipo derivato genera un'eccezione per evitare una modifica del mapping imprevista in futuro.</span><span class="sxs-lookup"><span data-stu-id="babf2-674">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-675">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-675">Why</span></span>

<span data-ttu-id="babf2-676">Attualmente il mapping di un tipo derivato in una tabella diversa non è un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="babf2-676">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="babf2-677">Questa modifica consente di evitare interruzioni future quando l'operazione diventerà un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="babf2-677">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-678">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-678">Mitigations</span></span>

<span data-ttu-id="babf2-679">Rimuovere qualsiasi tentativo di mapping di tipi derivati in altre tabelle.</span><span class="sxs-lookup"><span data-stu-id="babf2-679">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="babf2-680">ForSqlServerHasIndex sostituito con HasIndex</span><span class="sxs-lookup"><span data-stu-id="babf2-680">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="babf2-681">Problema n. 12366</span><span class="sxs-lookup"><span data-stu-id="babf2-681">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-682">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-682">Old behavior</span></span>

<span data-ttu-id="babf2-683">Nelle versioni precedenti a EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offriva un metodo per configurare le colonne usate con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="babf2-683">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-684">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-684">New behavior</span></span>

<span data-ttu-id="babf2-685">A partire da EF Core 3.0, l'uso di `Include` in un indice è supportato a livello relazionale.</span><span class="sxs-lookup"><span data-stu-id="babf2-685">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="babf2-686">Usare `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="babf2-686">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-687">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-687">Why</span></span>

<span data-ttu-id="babf2-688">Questa modifica è stata apportata per consolidare l'API per gli indici con `Include` in un'unica posizione per tutti i provider di database.</span><span class="sxs-lookup"><span data-stu-id="babf2-688">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-689">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-689">Mitigations</span></span>

<span data-ttu-id="babf2-690">Usare la nuova API, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-690">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="babf2-691">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="babf2-691">Metadata API changes</span></span>

[<span data-ttu-id="babf2-692">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="babf2-692">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="babf2-693">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-693">New behavior</span></span>

<span data-ttu-id="babf2-694">Le proprietà seguenti sono state convertite in metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="babf2-694">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="babf2-695">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-695">Why</span></span>

<span data-ttu-id="babf2-696">Questa modifica semplifica l'implementazione delle interfacce menzionate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-696">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-697">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-697">Mitigations</span></span>

<span data-ttu-id="babf2-698">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="babf2-698">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="babf2-699">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="babf2-699">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="babf2-700">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="babf2-700">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="babf2-701">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-701">New behavior</span></span>

<span data-ttu-id="babf2-702">I metodi di estensione specifici del provider verranno resi flat:</span><span class="sxs-lookup"><span data-stu-id="babf2-702">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="babf2-703">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-703">Why</span></span>

<span data-ttu-id="babf2-704">Questa modifica semplifica l'implementazione dei metodi di estensione menzionati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="babf2-704">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-705">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-705">Mitigations</span></span>

<span data-ttu-id="babf2-706">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="babf2-706">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="babf2-707">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-707">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="babf2-708">Problema n. 12151</span><span class="sxs-lookup"><span data-stu-id="babf2-708">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-709">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-709">Old behavior</span></span>

<span data-ttu-id="babf2-710">Nelle versioni precedenti a EF Core 3.0, EF Core inviava `PRAGMA foreign_keys = 1` quando veniva aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="babf2-710">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-711">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-711">New behavior</span></span>

<span data-ttu-id="babf2-712">A partire da EF Core 3.0, EF Core non invia più `PRAGMA foreign_keys = 1` quando viene aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="babf2-712">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-713">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-713">Why</span></span>

<span data-ttu-id="babf2-714">Questa modifica è stata apportata poiché EF Core usa `SQLitePCLRaw.bundle_e_sqlite3` per impostazione predefinita. Ciò significa che l'imposizione della chiave esterna è abilitata per impostazione predefinita e non deve essere abilitata in modo esplicito ogni volta che viene aperta una connessione.</span><span class="sxs-lookup"><span data-stu-id="babf2-714">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-715">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-715">Mitigations</span></span>

<span data-ttu-id="babf2-716">Le chiavi esterne sono abilitate per impostazione predefinita in SQLitePCLRaw.bundle_e_sqlite3, usato per impostazione predefinita per EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-716">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="babf2-717">Per gli altri casi, è possibile abilitare le chiavi esterne specificando `Foreign Keys=True` nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="babf2-717">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="babf2-718">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="babf2-718">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="babf2-719">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-719">Old behavior</span></span>

<span data-ttu-id="babf2-720">Nelle versioni precedenti a EF Core 3.0, EF Core usava `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="babf2-720">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-721">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-721">New behavior</span></span>

<span data-ttu-id="babf2-722">A partire da EF Core 3.0, EF Core usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="babf2-722">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-723">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-723">Why</span></span>

<span data-ttu-id="babf2-724">Questa modifica è stata apportata per rendere coerente la versione di SQLite usata in iOS con le altre piattaforme.</span><span class="sxs-lookup"><span data-stu-id="babf2-724">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-725">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-725">Mitigations</span></span>

<span data-ttu-id="babf2-726">Per usare la versione di SQLite nativa in iOS, configurare `Microsoft.Data.Sqlite` per l'uso di un'aggregazione `SQLitePCLRaw` diversa.</span><span class="sxs-lookup"><span data-stu-id="babf2-726">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="babf2-727">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-727">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="babf2-728">Problema n. 15078</span><span class="sxs-lookup"><span data-stu-id="babf2-728">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-729">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-729">Old behavior</span></span>

<span data-ttu-id="babf2-730">I valori Guid in precedenza venivano archiviati come valori BLOB in SQLite.</span><span class="sxs-lookup"><span data-stu-id="babf2-730">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-731">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-731">New behavior</span></span>

<span data-ttu-id="babf2-732">I valori Guid vengono ora archiviati come TEXT.</span><span class="sxs-lookup"><span data-stu-id="babf2-732">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-733">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-733">Why</span></span>

<span data-ttu-id="babf2-734">Il formato binario dei valori Guid non è standardizzato.</span><span class="sxs-lookup"><span data-stu-id="babf2-734">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="babf2-735">L'archiviazione dei valori come TEXT rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="babf2-735">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-736">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-736">Mitigations</span></span>

<span data-ttu-id="babf2-737">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="babf2-737">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="babf2-738">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-738">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="babf2-739">Microsoft.Data.Sqlite rimane in grado di leggere i valori Guid sia da colonne BLOB che TEXT. Tuttavia, poiché è stato modificato il formato predefinito per i parametri e le costanti, probabilmente sarà necessario intervenire per la maggior parte degli scenari che coinvolgono valori Guid.</span><span class="sxs-lookup"><span data-stu-id="babf2-739">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="babf2-740">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="babf2-740">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="babf2-741">Problema n. 15020</span><span class="sxs-lookup"><span data-stu-id="babf2-741">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-742">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-742">Old behavior</span></span>

<span data-ttu-id="babf2-743">I valori char in precedenza venivano archiviati come valori interi in SQLite.</span><span class="sxs-lookup"><span data-stu-id="babf2-743">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="babf2-744">Un valore char di *A* veniva ad esempio archiviato come valore intero 65.</span><span class="sxs-lookup"><span data-stu-id="babf2-744">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-745">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-745">New behavior</span></span>

<span data-ttu-id="babf2-746">I valori char vengono ora archiviati come testo.</span><span class="sxs-lookup"><span data-stu-id="babf2-746">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-747">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-747">Why</span></span>

<span data-ttu-id="babf2-748">L'archiviazione dei valori come testo è un'operazione più naturale e rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="babf2-748">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-749">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-749">Mitigations</span></span>

<span data-ttu-id="babf2-750">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="babf2-750">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="babf2-751">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="babf2-751">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="babf2-752">Microsoft.Data.Sqlite rimane comunque in grado di leggere i valori di caratteri presenti sia nelle colonne di valori interi sia in quelle di testo, quindi alcuni scenari potrebbero non richiedere alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="babf2-752">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="babf2-753">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="babf2-753">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="babf2-754">Problema n. 12978</span><span class="sxs-lookup"><span data-stu-id="babf2-754">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-755">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-755">Old behavior</span></span>

<span data-ttu-id="babf2-756">Gli ID di migrazione venivano inavvertitamente generati usando il calendario delle impostazioni cultura correnti.</span><span class="sxs-lookup"><span data-stu-id="babf2-756">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-757">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-757">New behavior</span></span>

<span data-ttu-id="babf2-758">Gli ID di migrazione ora vengono sempre generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica (calendario gregoriano).</span><span class="sxs-lookup"><span data-stu-id="babf2-758">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-759">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-759">Why</span></span>

<span data-ttu-id="babf2-760">L'ordine delle migrazioni è importante quando si esegue l'aggiornamento del database o si risolvono i conflitti di unione.</span><span class="sxs-lookup"><span data-stu-id="babf2-760">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="babf2-761">L'uso del calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica evita i problemi che possono verificarsi quando i membri del team hanno calendari di sistema diversi.</span><span class="sxs-lookup"><span data-stu-id="babf2-761">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-762">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-762">Mitigations</span></span>

<span data-ttu-id="babf2-763">Questa modifica interessa gli utenti che usano un calendario non gregoriano in cui l'anno ha un'estensione superiore al calendario gregoriano (come il calendario buddista tailandese).</span><span class="sxs-lookup"><span data-stu-id="babf2-763">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="babf2-764">Gli ID di migrazione esistenti dovranno essere aggiornati in modo che le nuove migrazioni vengano collocate dopo le migrazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-764">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="babf2-765">L'ID di migrazione è disponibile nell'attributo di migrazione presente nei file di progettazione delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-765">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="babf2-766">È necessario aggiornare anche la tabella della cronologia delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-766">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="babf2-767">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="babf2-767">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="babf2-768">Problema n. 16400</span><span class="sxs-lookup"><span data-stu-id="babf2-768">Tracking Issue #16400</span></span>](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-769">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-769">Old behavior</span></span>

<span data-ttu-id="babf2-770">Prima di EF Core 3.0 si poteva usare `UseRowNumberForPaging` per generare codice SQL per la suddivisione in pagine compatibile con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="babf2-770">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-771">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-771">New behavior</span></span>

<span data-ttu-id="babf2-772">A partire da EF Core 3.0, EF genererà solo codice SQL per la suddivisione in pagine compatibile solo con versioni successive di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="babf2-772">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-773">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-773">Why</span></span>

<span data-ttu-id="babf2-774">Questa modifica è stata apportata perché [SQL Server 2008 non è più un prodotto supportato](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) e l'aggiornamento di questa funzionalità per interagire con le modifiche apportate per le query in EF Core 3.0 è un lavoro significativo.</span><span class="sxs-lookup"><span data-stu-id="babf2-774">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-775">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-775">Mitigations</span></span>

<span data-ttu-id="babf2-776">È consigliabile eseguire l'aggiornamento a una versione più recente di SQL Server o usare un livello di compatibilità superiore, in modo che il codice SQL generato sia supportato.</span><span class="sxs-lookup"><span data-stu-id="babf2-776">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="babf2-777">Detto questo, se non è possibile procedere in questo modo, [aggiungere un commento per il problema](https://github.com/dotnet/efcore/issues/16400) con indicazioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="babf2-777">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="babf2-778">Microsoft potrebbe rivedere questa decisione in base ai commenti e suggerimenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-778">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="babf2-779">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="babf2-779">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="babf2-780">Problema n. 16119</span><span class="sxs-lookup"><span data-stu-id="babf2-780">Tracking Issue #16119</span></span>](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-781">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-781">Old behavior</span></span>

<span data-ttu-id="babf2-782">`IDbContextOptionsExtension` conteneva metodi per fornire i metadati relativi all'estensione.</span><span class="sxs-lookup"><span data-stu-id="babf2-782">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-783">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-783">New behavior</span></span>

<span data-ttu-id="babf2-784">Questi metodi sono stati spostati in una nuova classe di base astratta `DbContextOptionsExtensionInfo`, restituita da una nuova proprietà `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="babf2-784">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-785">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-785">Why</span></span>

<span data-ttu-id="babf2-786">Nelle versioni dalla 2.0 alla 3.0 è stato necessario aggiungere o modificare questi metodi più volte.</span><span class="sxs-lookup"><span data-stu-id="babf2-786">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="babf2-787">Suddividendoli in una nuova classe di base astratta sarà più facile apportare questo tipo di modifiche senza compromettere il funzionamento delle estensioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-787">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-788">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-788">Mitigations</span></span>

<span data-ttu-id="babf2-789">Aggiornare le estensioni per seguire il nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="babf2-789">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="babf2-790">Sono disponibili esempi nelle numerose implementazioni di `IDbContextOptionsExtension` per diversi tipi di estensioni nel codice sorgente di EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-790">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="babf2-791">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="babf2-791">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="babf2-792">Problema n. 10985</span><span class="sxs-lookup"><span data-stu-id="babf2-792">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="babf2-793">Modifica</span><span class="sxs-lookup"><span data-stu-id="babf2-793">Change</span></span>

<span data-ttu-id="babf2-794">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` è stato rinominato in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="babf2-794">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-795">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-795">Why</span></span>

<span data-ttu-id="babf2-796">Allineamento del nome di questo evento di avviso con tutti gli altri eventi di avviso.</span><span class="sxs-lookup"><span data-stu-id="babf2-796">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-797">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-797">Mitigations</span></span>

<span data-ttu-id="babf2-798">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="babf2-798">Use the new name.</span></span> <span data-ttu-id="babf2-799">(Si noti che il numero di ID evento non è stato modificato.)</span><span class="sxs-lookup"><span data-stu-id="babf2-799">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="babf2-800">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="babf2-800">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="babf2-801">Problema n. 10730</span><span class="sxs-lookup"><span data-stu-id="babf2-801">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-802">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-802">Old behavior</span></span>

<span data-ttu-id="babf2-803">Prima di EF Core 3.0, si faceva riferimento ai nomi di vincolo di chiave esterna semplicemente con "Name".</span><span class="sxs-lookup"><span data-stu-id="babf2-803">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="babf2-804">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-804">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="babf2-805">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-805">New behavior</span></span>

<span data-ttu-id="babf2-806">A partire da EF Core 3.0, si fa ora riferimento ai nomi di vincolo di chiave esterna con "ConstraintName".</span><span class="sxs-lookup"><span data-stu-id="babf2-806">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="babf2-807">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-807">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="babf2-808">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-808">Why</span></span>

<span data-ttu-id="babf2-809">Questa modifica introduce coerenza per la denominazione in quest'area e chiarisce anche che si tratta del nome del vincolo di chiave esterna e non del nome della colonna o della proprietà per cui è definita la chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="babf2-809">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-810">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-810">Mitigations</span></span>

<span data-ttu-id="babf2-811">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="babf2-811">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="babf2-812">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="babf2-812">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="babf2-813">Problema n. 15997</span><span class="sxs-lookup"><span data-stu-id="babf2-813">Tracking Issue #15997</span></span>](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-814">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-814">Old behavior</span></span>

<span data-ttu-id="babf2-815">Prima di EF Core 3.0 questi metodi erano protetti.</span><span class="sxs-lookup"><span data-stu-id="babf2-815">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-816">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-816">New behavior</span></span>

<span data-ttu-id="babf2-817">A partire da EF Core 3.0 questi metodi sono pubblici.</span><span class="sxs-lookup"><span data-stu-id="babf2-817">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-818">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-818">Why</span></span>

<span data-ttu-id="babf2-819">Questi metodi vengono usati da EF per determinare se un database viene creato, ma vuoto.</span><span class="sxs-lookup"><span data-stu-id="babf2-819">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="babf2-820">Ciò risulta utile all'esterno di EF quando occorre determinare se applicare o meno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-820">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-821">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-821">Mitigations</span></span>

<span data-ttu-id="babf2-822">Modificare l'accessibilità di eventuali override.</span><span class="sxs-lookup"><span data-stu-id="babf2-822">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="babf2-823">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="babf2-823">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="babf2-824">Problema n. 11506</span><span class="sxs-lookup"><span data-stu-id="babf2-824">Tracking Issue #11506</span></span>](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-825">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-825">Old behavior</span></span>

<span data-ttu-id="babf2-826">Prima di EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un pacchetto NuGet normale ed era possibile fare riferimento al relativo assembly dai progetti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="babf2-826">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-827">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-827">New behavior</span></span>

<span data-ttu-id="babf2-828">A partire da EF Core 3.0 è un pacchetto DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="babf2-828">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="babf2-829">Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che non è più possibile, per impostazione predefinita, fare riferimento al relativo assembly.</span><span class="sxs-lookup"><span data-stu-id="babf2-829">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-830">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-830">Why</span></span>

<span data-ttu-id="babf2-831">Questo pacchetto è destinato solo all'uso in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-831">This package is only intended to be used at design time.</span></span> <span data-ttu-id="babf2-832">Le applicazioni distribuite non devono farvi riferimento.</span><span class="sxs-lookup"><span data-stu-id="babf2-832">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="babf2-833">Questa raccomandazione è rafforzata dall'impostazione del pacchetto come DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="babf2-833">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-834">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-834">Mitigations</span></span>

<span data-ttu-id="babf2-835">Se è necessario fare riferimento a questo pacchetto per eseguire l'override del comportamento della fase di progettazione di EF Core, è possibile aggiornare i metadati dell'elemento PackageReference nel progetto.</span><span class="sxs-lookup"><span data-stu-id="babf2-835">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="babf2-836">In presenza di riferimenti transitivi al pacchetto tramite Microsoft.EntityFrameworkCore.Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto per modificare i relativi metadati.</span><span class="sxs-lookup"><span data-stu-id="babf2-836">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="babf2-837">Un riferimento esplicito di questo tipo deve essere aggiunto a qualsiasi progetto in cui sono necessari i tipi del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="babf2-837">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="babf2-838">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="babf2-838">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="babf2-839">Problema n. 14824</span><span class="sxs-lookup"><span data-stu-id="babf2-839">Tracking Issue #14824</span></span>](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-840">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-840">Old behavior</span></span>

<span data-ttu-id="babf2-841">Microsoft.EntityFrameworkCore.Sqlite dipendeva in precedenza dalla versione 1.1.12 di SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="babf2-841">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-842">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-842">New behavior</span></span>

<span data-ttu-id="babf2-843">Il pacchetto è stato aggiornato in base alla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="babf2-843">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-844">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-844">Why</span></span>

<span data-ttu-id="babf2-845">La versione 2.0.0 di SQLitePCL.raw è destinata a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="babf2-845">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="babf2-846">Era in precedenza destinata a .NET Standard 1.1 e ciò richiedeva un notevole impegno di chiusura di pacchetti transitivi per il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="babf2-846">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-847">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-847">Mitigations</span></span>

<span data-ttu-id="babf2-848">SQLitePCL.raw versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-848">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="babf2-849">Per informazioni dettagliate, vedere le [Note sulla versione](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .</span><span class="sxs-lookup"><span data-stu-id="babf2-849">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="babf2-850">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="babf2-850">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="babf2-851">Problema n. 14825</span><span class="sxs-lookup"><span data-stu-id="babf2-851">Tracking Issue #14825</span></span>](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-852">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-852">Old behavior</span></span>

<span data-ttu-id="babf2-853">I pacchetti spaziali dipendevano in precedenza dalla versione 1.15.1 di NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="babf2-853">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-854">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-854">New behavior</span></span>

<span data-ttu-id="babf2-855">Il pacchetto è stato aggiornato in modo da dipendere dalla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="babf2-855">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-856">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-856">Why</span></span>

<span data-ttu-id="babf2-857">La versione 2.0.0 di NetTopologySuite risolve vari problemi di usabilità riscontrati dagli utenti di EF Core.</span><span class="sxs-lookup"><span data-stu-id="babf2-857">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-858">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-858">Mitigations</span></span>

<span data-ttu-id="babf2-859">NetTopologySuite versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="babf2-859">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="babf2-860">Per informazioni dettagliate, vedere le [Note sulla versione](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .</span><span class="sxs-lookup"><span data-stu-id="babf2-860">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="babf2-861">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="babf2-861">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="babf2-862">Rilevamento del problema #15636</span><span class="sxs-lookup"><span data-stu-id="babf2-862">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-863">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-863">Old behavior</span></span>

<span data-ttu-id="babf2-864">Microsoft. EntityFrameworkCore. SqlServer in precedenza dipende da System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="babf2-864">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="babf2-865">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-865">New behavior</span></span>

<span data-ttu-id="babf2-866">Il pacchetto è stato aggiornato in base a Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="babf2-866">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-867">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-867">Why</span></span>

<span data-ttu-id="babf2-868">Microsoft. Data. SqlClient è il driver di accesso ai dati principale per SQL Server in futuro e System. Data. SqlClient non è più l'obiettivo dello sviluppo.</span><span class="sxs-lookup"><span data-stu-id="babf2-868">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="babf2-869">Alcune funzionalità importanti, ad esempio Always Encrypted, sono disponibili solo in Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="babf2-869">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-870">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-870">Mitigations</span></span>

<span data-ttu-id="babf2-871">Se il codice prende una dipendenza diretta da System. Data. SqlClient, è necessario modificarlo in modo che faccia riferimento a Microsoft. Data. SqlClient. Poiché i due pacchetti gestiscono un livello molto elevato di compatibilità API, questo dovrebbe essere solo una semplice modifica del pacchetto e dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="babf2-871">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="babf2-872">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="babf2-872">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="babf2-873">Problema n. 13573</span><span class="sxs-lookup"><span data-stu-id="babf2-873">Tracking Issue #13573</span></span>](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-874">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-874">Old behavior</span></span>

<span data-ttu-id="babf2-875">Un tipo di entità con più proprietà di navigazione unidirezionale che fanno riferimento a se stesse e più chiavi esterne corrispondenti è stato erroneamente configurato come relazione singola.</span><span class="sxs-lookup"><span data-stu-id="babf2-875">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="babf2-876">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-876">For example:</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="babf2-877">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-877">New behavior</span></span>

<span data-ttu-id="babf2-878">Questo scenario viene ora rilevato nella compilazione del modello e viene generata un'eccezione indicante che il modello è ambiguo.</span><span class="sxs-lookup"><span data-stu-id="babf2-878">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-879">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-879">Why</span></span>

<span data-ttu-id="babf2-880">Il modello risultante era ambiguo e sarà probabilmente errato in questo caso.</span><span class="sxs-lookup"><span data-stu-id="babf2-880">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-881">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-881">Mitigations</span></span>

<span data-ttu-id="babf2-882">Usare la configurazione completa della relazione.</span><span class="sxs-lookup"><span data-stu-id="babf2-882">Use full configuration of the relationship.</span></span> <span data-ttu-id="babf2-883">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="babf2-883">For example:</span></span>

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

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="babf2-884">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="babf2-884">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="babf2-885">Rilevamento del problema #12757</span><span class="sxs-lookup"><span data-stu-id="babf2-885">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="babf2-886">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="babf2-886">Old behavior</span></span>

<span data-ttu-id="babf2-887">Un DbFunction configurato con schema come stringa vuota è stato trattato come funzione predefinita senza uno schema.</span><span class="sxs-lookup"><span data-stu-id="babf2-887">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="babf2-888">Il codice seguente, ad esempio, eseguirà il mapping della `DatePart` funzione CLR alla `DATEPART` funzione predefinita in SqlServer.</span><span class="sxs-lookup"><span data-stu-id="babf2-888">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="babf2-889">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="babf2-889">New behavior</span></span>

<span data-ttu-id="babf2-890">Tutti i mapping di DbFunction sono considerati mappati alle funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="babf2-890">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="babf2-891">Pertanto, il valore stringa vuoto inserisce la funzione all'interno dello schema predefinito per il modello.</span><span class="sxs-lookup"><span data-stu-id="babf2-891">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="babf2-892">Che può corrispondere allo schema configurato in modo esplicito tramite l'API Fluent `modelBuilder.HasDefaultSchema()` o `dbo` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="babf2-892">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="babf2-893">Perché</span><span class="sxs-lookup"><span data-stu-id="babf2-893">Why</span></span>

<span data-ttu-id="babf2-894">Lo schema precedentemente vuoto era un modo per trattare la funzione è incorporata, ma tale logica è applicabile solo per SqlServer, in cui le funzioni predefinite non appartengono ad alcuno schema.</span><span class="sxs-lookup"><span data-stu-id="babf2-894">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="babf2-895">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="babf2-895">Mitigations</span></span>

<span data-ttu-id="babf2-896">Configurare manualmente la conversione di DbFunction per eseguirne il mapping a una funzione predefinita.</span><span class="sxs-lookup"><span data-stu-id="babf2-896">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="babf2-897">~~EF Core 3,0 destinazioni .NET Standard 2,1 anziché .NET Standard 2,0~~ Ripristinato</span><span class="sxs-lookup"><span data-stu-id="babf2-897">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

[<span data-ttu-id="babf2-898">Problema n. 15498</span><span class="sxs-lookup"><span data-stu-id="babf2-898">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

<span data-ttu-id="babf2-899">EF Core 3,0 destinazioni .NET Standard 2,1, che è una modifica di rilievo che esclude .NET Framework applicazioni.</span><span class="sxs-lookup"><span data-stu-id="babf2-899">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="babf2-900">EF Core 3,1 è stato ripristinato e le destinazioni .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="babf2-900">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="babf2-901">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="babf2-901">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="babf2-902">Problema n. 14523</span><span class="sxs-lookup"><span data-stu-id="babf2-902">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="babf2-903">Questa modifica è stata annullata perché la nuova configurazione in EF Core 3.0 consente all'applicazione di specificare il livello di log per qualsiasi evento.</span><span class="sxs-lookup"><span data-stu-id="babf2-903">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="babf2-904">Ad esempio, per impostare la registrazione di SQL sul livello `Debug`, configurare il livello in modo esplicito in `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="babf2-904">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
