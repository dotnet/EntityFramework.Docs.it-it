---
title: Modifiche di rilievo in EF Core 3. x-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: e348cb630d91ebe4536b73b9a7bd9a7b6a46db79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072239"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="c78a6-103">Modifiche di rilievo incluse in EF Core 3. x</span><span class="sxs-lookup"><span data-stu-id="c78a6-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="c78a6-104">Le modifiche alle API e al comportamento seguenti possono causare l'interruzione delle applicazioni esistenti durante l'aggiornamento a 3. x.</span><span class="sxs-lookup"><span data-stu-id="c78a6-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="c78a6-105">Le modifiche che si prevede abbiano impatto solo sui provider di database sono documentate nelle [modifiche che influiscono sul provider](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="c78a6-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="c78a6-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="c78a6-106">Summary</span></span>

| <span data-ttu-id="c78a6-107">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="c78a6-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="c78a6-108">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="c78a6-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="c78a6-109">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="c78a6-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="c78a6-110">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-110">High</span></span>       |
| [<span data-ttu-id="c78a6-111">EF Core 3.0 usa come destinazione .NET Standard 2.1 invece che .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="c78a6-112">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-112">High</span></span>      |
| [<span data-ttu-id="c78a6-113">Lo strumento da riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="c78a6-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="c78a6-114">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-114">High</span></span>      |
| [<span data-ttu-id="c78a6-115">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="c78a6-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="c78a6-116">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-116">High</span></span>      |
| [<span data-ttu-id="c78a6-117">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="c78a6-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="c78a6-118">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-118">High</span></span>      |
| [<span data-ttu-id="c78a6-119">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="c78a6-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="c78a6-120">Alto</span><span class="sxs-lookup"><span data-stu-id="c78a6-120">High</span></span>      |
| [<span data-ttu-id="c78a6-121">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c78a6-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="c78a6-122">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-122">Medium</span></span>      |
| [<span data-ttu-id="c78a6-123">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="c78a6-124">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-124">Medium</span></span>      |
| [<span data-ttu-id="c78a6-125">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="c78a6-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="c78a6-126">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-126">Medium</span></span>      |
| [<span data-ttu-id="c78a6-127">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="c78a6-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="c78a6-128">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-128">Medium</span></span>      |
| [<span data-ttu-id="c78a6-129">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="c78a6-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="c78a6-130">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-130">Medium</span></span>      |
| [<span data-ttu-id="c78a6-131">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="c78a6-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="c78a6-132">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-132">Medium</span></span>      |
| [<span data-ttu-id="c78a6-133">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="c78a6-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="c78a6-134">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-134">Medium</span></span>      |
| [<span data-ttu-id="c78a6-135">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="c78a6-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="c78a6-136">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-136">Medium</span></span>      |
| [<span data-ttu-id="c78a6-137">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="c78a6-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="c78a6-138">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-138">Medium</span></span>      |
| [<span data-ttu-id="c78a6-139">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="c78a6-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="c78a6-140">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-140">Medium</span></span>      |
| [<span data-ttu-id="c78a6-141">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="c78a6-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="c78a6-142">Media</span><span class="sxs-lookup"><span data-stu-id="c78a6-142">Medium</span></span>      |
| [<span data-ttu-id="c78a6-143">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="c78a6-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="c78a6-144">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-144">Low</span></span>      |
| [<span data-ttu-id="c78a6-145">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="c78a6-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="c78a6-146">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-146">Low</span></span>      |
| [<span data-ttu-id="c78a6-147">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="c78a6-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="c78a6-148">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-148">Low</span></span>      |
| [<span data-ttu-id="c78a6-149">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="c78a6-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="c78a6-150">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-150">Low</span></span>      |
| [<span data-ttu-id="c78a6-151">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="c78a6-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="c78a6-152">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-152">Low</span></span>      |
| [<span data-ttu-id="c78a6-153">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="c78a6-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="c78a6-154">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-154">Low</span></span>      |
| [<span data-ttu-id="c78a6-155">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="c78a6-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="c78a6-156">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-156">Low</span></span>      |
| [<span data-ttu-id="c78a6-157">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="c78a6-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="c78a6-158">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-158">Low</span></span>      |
| [<span data-ttu-id="c78a6-159">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="c78a6-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="c78a6-160">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-160">Low</span></span>      |
| [<span data-ttu-id="c78a6-161">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="c78a6-162">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-162">Low</span></span>      |
| [<span data-ttu-id="c78a6-163">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="c78a6-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="c78a6-164">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-164">Low</span></span>      |
| [<span data-ttu-id="c78a6-165">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="c78a6-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="c78a6-166">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-166">Low</span></span>      |
| [<span data-ttu-id="c78a6-167">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="c78a6-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="c78a6-168">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-168">Low</span></span>      |
| [<span data-ttu-id="c78a6-169">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="c78a6-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="c78a6-170">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-170">Low</span></span>      |
| [<span data-ttu-id="c78a6-171">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="c78a6-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="c78a6-172">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-172">Low</span></span>      |
| [<span data-ttu-id="c78a6-173">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="c78a6-174">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-174">Low</span></span>      |
| [<span data-ttu-id="c78a6-175">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="c78a6-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="c78a6-176">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-176">Low</span></span>      |
| [<span data-ttu-id="c78a6-177">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="c78a6-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="c78a6-178">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-178">Low</span></span>      |
| [<span data-ttu-id="c78a6-179">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="c78a6-180">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-180">Low</span></span>      |
| [<span data-ttu-id="c78a6-181">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="c78a6-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="c78a6-182">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-182">Low</span></span>      |
| [<span data-ttu-id="c78a6-183">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="c78a6-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="c78a6-184">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-184">Low</span></span>      |
| [<span data-ttu-id="c78a6-185">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="c78a6-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="c78a6-186">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-186">Low</span></span>      |
| [<span data-ttu-id="c78a6-187">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="c78a6-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="c78a6-188">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-188">Low</span></span>      |
| [<span data-ttu-id="c78a6-189">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="c78a6-190">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-190">Low</span></span>      |
| [<span data-ttu-id="c78a6-191">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="c78a6-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="c78a6-192">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-192">Low</span></span>      |
| [<span data-ttu-id="c78a6-193">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="c78a6-194">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-194">Low</span></span>      |
| [<span data-ttu-id="c78a6-195">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="c78a6-196">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-196">Low</span></span>      |
| [<span data-ttu-id="c78a6-197">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="c78a6-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="c78a6-198">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-198">Low</span></span>      |
| [<span data-ttu-id="c78a6-199">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="c78a6-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="c78a6-200">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-200">Low</span></span>      |
| [<span data-ttu-id="c78a6-201">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="c78a6-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="c78a6-202">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-202">Low</span></span>      |
| [<span data-ttu-id="c78a6-203">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="c78a6-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="c78a6-204">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-204">Low</span></span>      |
| [<span data-ttu-id="c78a6-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="c78a6-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="c78a6-206">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-206">Low</span></span>      |
| [<span data-ttu-id="c78a6-207">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="c78a6-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="c78a6-208">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-208">Low</span></span>      |
| [<span data-ttu-id="c78a6-209">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="c78a6-210">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-210">Low</span></span>      |
| [<span data-ttu-id="c78a6-211">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="c78a6-212">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-212">Low</span></span>      |
| [<span data-ttu-id="c78a6-213">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="c78a6-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="c78a6-214">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-214">Low</span></span>      |
| [<span data-ttu-id="c78a6-215">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="c78a6-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="c78a6-216">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-216">Low</span></span>      |
| [<span data-ttu-id="c78a6-217">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="c78a6-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="c78a6-218">Basso</span><span class="sxs-lookup"><span data-stu-id="c78a6-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="c78a6-219">Le query LINQ non vengono più valutate nel client</span><span class="sxs-lookup"><span data-stu-id="c78a6-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="c78a6-220">[Rilevamento del problema #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935) 
 [Vedere anche problema #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="c78a6-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="c78a6-221">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-221">**Old behavior**</span></span>

<span data-ttu-id="c78a6-222">Nelle versioni precedenti alla versione 3.0, quando EF Core non era in grado di convertire un'espressione inclusa in una query in SQL o in un parametro, l'espressione veniva automaticamente valutata nel client.</span><span class="sxs-lookup"><span data-stu-id="c78a6-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="c78a6-223">Per impostazione predefinita, la valutazione client di espressioni potenzialmente dispendiose si limitava ad attivare solo un avviso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="c78a6-224">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-224">**New behavior**</span></span>

<span data-ttu-id="c78a6-225">A partire dalla versione 3.0, EF Core consente solo la valutazione delle espressioni nella proiezione di primo livello (l'ultima chiamata `Select()` nella query) nel client.</span><span class="sxs-lookup"><span data-stu-id="c78a6-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="c78a6-226">Quando le espressioni in altre posizioni all'interno della query non possono essere convertite in SQL o in un parametro, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="c78a6-227">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-227">**Why**</span></span>

<span data-ttu-id="c78a6-228">La valutazione client automatica delle query consente di eseguire numerose query anche nel caso in cui parti importanti delle query non possono essere convertite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="c78a6-229">Questo comportamento può causare un comportamento imprevisto e potenzialmente dannoso che può diventare evidente solo in produzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="c78a6-230">Ad esempio, una condizione in una chiamata `Where()` che non può essere convertita può causare il trasferimento di tutte le righe della tabella del server di database e l'applicazione del filtro nel client.</span><span class="sxs-lookup"><span data-stu-id="c78a6-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="c78a6-231">È probabile che questa situazione non venga rilevata se la tabella contiene solo alcune righe in fase di sviluppo, ma che abbia un grande impatto quando l'applicazione passa in produzione dove la tabella può contenere milioni di righe.</span><span class="sxs-lookup"><span data-stu-id="c78a6-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="c78a6-232">Gli avvisi di valutazione client inoltre si sono rivelati molto facili da ignorare durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="c78a6-233">Inoltre, la valutazione client automatica può causare problemi in cui il miglioramento della conversione di query per espressioni specifiche causa modifiche impreviste che causano un'interruzione da una versione all'altra.</span><span class="sxs-lookup"><span data-stu-id="c78a6-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="c78a6-234">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-234">**Mitigations**</span></span>

<span data-ttu-id="c78a6-235">Se una query non può essere convertita completamente, riscrivere la query in un formato che possa essere convertito o usare `AsEnumerable()`, `ToList()` o un elemento simile per riportare in modo esplicito i dati nel client dove possono essere quindi ulteriormente elaborati usando LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="c78a6-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="c78a6-236">EF Core 3.0 usa come destinazione .NET Standard 2.1 invece che .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="c78a6-237">Problema n. 15498</span><span class="sxs-lookup"><span data-stu-id="c78a6-237">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="c78a6-238">EF Core 3,1 di nuovo la destinazione .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="c78a6-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="c78a6-239">Questa operazione riporta il supporto per .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c78a6-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="c78a6-240">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-240">**Old behavior**</span></span>

<span data-ttu-id="c78a6-241">Prima della versione 3.0, EF Core usava come destinazione .NET Standard 2.0 e poteva essere eseguito in tutte le piattaforme che supportano tale standard, incluso .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c78a6-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="c78a6-242">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-242">**New behavior**</span></span>

<span data-ttu-id="c78a6-243">A partire dalla versione 3.0, EF Core usa come destinazione .NET Standard 2.1 e verrà eseguito in tutte le piattaforme che supportano questo standard.</span><span class="sxs-lookup"><span data-stu-id="c78a6-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="c78a6-244">.NET Framework non è incluso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="c78a6-245">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-245">**Why**</span></span>

<span data-ttu-id="c78a6-246">Questo comportamento deriva da una decisione strategica per le tecnologie .NET, che mira a concentrare le energie su .NET Core e su altre piattaforme .NET moderne, ad esempio Xamarin.</span><span class="sxs-lookup"><span data-stu-id="c78a6-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="c78a6-247">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-247">**Mitigations**</span></span>

<span data-ttu-id="c78a6-248">Usare EF Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="c78a6-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="c78a6-249">Entity Framework Core non è più incluso nel framework condiviso di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c78a6-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="c78a6-250">Annunci problema n. 325</span><span class="sxs-lookup"><span data-stu-id="c78a6-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="c78a6-251">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-251">**Old behavior**</span></span>

<span data-ttu-id="c78a6-252">Nelle versioni precedenti ad ASP.NET Core 3.0, quando si aggiungeva un riferimento a un pacchetto in `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, veniva inserito EF Core e alcuni dei provider di dati di EF Core come il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c78a6-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="c78a6-253">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-253">**New behavior**</span></span>

<span data-ttu-id="c78a6-254">A partire dalla versione 3.0, il framework condiviso di ASP.NET Core non include EF Core o provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="c78a6-255">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-255">**Why**</span></span>

<span data-ttu-id="c78a6-256">Prima di questa modifica, per ottenere EF Core erano necessarie procedure diverse, a seconda che l'applicazione avesse o meno come destinazione ASP.NET Core e SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c78a6-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="c78a6-257">Inoltre, l'aggiornamento di ASP.NET Core forzava l'aggiornamento di EF Core e del provider di SQL Server, non sempre auspicabile.</span><span class="sxs-lookup"><span data-stu-id="c78a6-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="c78a6-258">Con questa modifica, la procedura per ottenere EF Core è la stessa in tutti i provider, le implementazioni .NET supportate e i tipi di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="c78a6-259">Gli sviluppatori possono ora controllare anche in modo preciso quando vengono aggiornati EF Core e i provider di dati di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="c78a6-260">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-260">**Mitigations**</span></span>

<span data-ttu-id="c78a6-261">Per usare EF Core in un'applicazione ASP.NET Core 3.0 o in un'altra applicazione supportata, aggiungere in modo esplicito un riferimento al pacchetto al provider di database di EF Core che verrà usato dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="c78a6-262">Lo strumento della riga di comando di EF Core, dotnet ef, non è più incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="c78a6-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="c78a6-263">Problema n. 14016</span><span class="sxs-lookup"><span data-stu-id="c78a6-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="c78a6-264">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-264">**Old behavior**</span></span>

<span data-ttu-id="c78a6-265">Prima della versione 3.0, lo strumento `dotnet ef` era incluso in .NET Core SDK ed era immediatamente disponibile dalla riga di comando di qualsiasi progetto senza richiedere passaggi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="c78a6-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="c78a6-266">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-266">**New behavior**</span></span>

<span data-ttu-id="c78a6-267">A partire dalla versione 3.0, .NET SDK non include lo strumento `dotnet ef` pertanto, prima di poterlo usare, è necessario installarlo in modo esplicito come strumento locale o globale.</span><span class="sxs-lookup"><span data-stu-id="c78a6-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="c78a6-268">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-268">**Why**</span></span>

<span data-ttu-id="c78a6-269">Questa modifica consente di distribuire e aggiornare `dotnet ef` come uno strumento della riga di comando di .NET in NuGet, coerentemente con il fatto che anche EF Core 3.0 viene distribuito come pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="c78a6-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="c78a6-270">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-270">**Mitigations**</span></span>

<span data-ttu-id="c78a6-271">Per essere in grado di gestire le migrazioni o eseguire lo scaffolding di `DbContext`, installare `dotnet-ef` come strumento globale:</span><span class="sxs-lookup"><span data-stu-id="c78a6-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="c78a6-272">È inoltre possibile ottenerlo come strumento locale quando si ripristinano le dipendenze di un progetto che lo dichiara come dipendenza di strumenti utilizzando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="c78a6-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="c78a6-273">I metodi FromSql, ExecuteSql ed ExecuteSqlAsync sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="c78a6-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="c78a6-274">Problema n. 10996</span><span class="sxs-lookup"><span data-stu-id="c78a6-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="c78a6-275">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-275">**Old behavior**</span></span>

<span data-ttu-id="c78a6-276">Prima di EF Core 3.0, erano disponibili overload per questi nomi di metodo per supportare l'uso con una stringa normale o una stringa che deve essere interpolata in SQL e parametri.</span><span class="sxs-lookup"><span data-stu-id="c78a6-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="c78a6-277">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-277">**New behavior**</span></span>

<span data-ttu-id="c78a6-278">A partire da EF Core 3.0, usare `FromSqlRaw`, `ExecuteSqlRaw` e `ExecuteSqlRawAsync` per creare una query con parametri in cui i parametri vengono passati separatamente dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="c78a6-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="c78a6-279">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="c78a6-280">Usare `FromSqlInterpolated`, `ExecuteSqlInterpolated`, e `ExecuteSqlInterpolatedAsync` per creare una query con parametri in cui i parametri vengono passati come parte di una stringa di query interpolata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="c78a6-281">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="c78a6-282">Si noti che entrambe le query precedenti produrranno lo stesso codice SQL con parametri con gli stessi parametri SQL.</span><span class="sxs-lookup"><span data-stu-id="c78a6-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="c78a6-283">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-283">**Why**</span></span>

<span data-ttu-id="c78a6-284">Con gli overload di metodi come questi, è molto facile chiamare accidentalmente il metodo con stringa non elaborata anche se l'intento era chiamare il metodo con stringa interpolata e viceversa.</span><span class="sxs-lookup"><span data-stu-id="c78a6-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="c78a6-285">Il risultato potrebbero essere query senza parametri, quando invece è prevista la parametrizzazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="c78a6-286">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-286">**Mitigations**</span></span>

<span data-ttu-id="c78a6-287">Passare all'uso dei nuovi nomi di metodo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="c78a6-288">Non è possibile comporre il metodo dati da tabelle se usato con stored procedure</span><span class="sxs-lookup"><span data-stu-id="c78a6-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="c78a6-289">Rilevamento del problema #15392</span><span class="sxs-lookup"><span data-stu-id="c78a6-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="c78a6-290">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-290">**Old behavior**</span></span>

<span data-ttu-id="c78a6-291">Prima di EF Core 3,0, il metodo dati da tabelle ha tentato di rilevare se il SQL passato può essere composto in base a.</span><span class="sxs-lookup"><span data-stu-id="c78a6-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="c78a6-292">Ha fatto la valutazione del client quando SQL era non componibile come un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="c78a6-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="c78a6-293">La query seguente ha funzionato eseguendo il stored procedure sul server e FirstOrDefault sul lato client.</span><span class="sxs-lookup"><span data-stu-id="c78a6-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="c78a6-294">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-294">**New behavior**</span></span>

<span data-ttu-id="c78a6-295">A partire da EF Core 3,0, EF Core non tenterà di analizzare SQL.</span><span class="sxs-lookup"><span data-stu-id="c78a6-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="c78a6-296">Quindi, se si esegue la composizione dopo FromSqlRaw/FromSqlInterpolated, EF Core comporrà il SQL causando una sottoquery.</span><span class="sxs-lookup"><span data-stu-id="c78a6-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="c78a6-297">Quindi, se si usa un stored procedure con Composition, si otterrà un'eccezione per la sintassi SQL non valida.</span><span class="sxs-lookup"><span data-stu-id="c78a6-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="c78a6-298">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-298">**Why**</span></span>

<span data-ttu-id="c78a6-299">EF Core 3,0 non supporta la valutazione automatica del client perché è stata soggetta a errori, come illustrato [qui](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="c78a6-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="c78a6-300">**Mitigazione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-300">**Mitigation**</span></span>

<span data-ttu-id="c78a6-301">Se si usa un stored procedure in FromSqlRaw/FromSqlInterpolated, è noto che non è possibile componerlo, quindi è possibile aggiungere __AsEnumerable/AsAsyncEnumerable__ subito dopo la chiamata al metodo dati da tabelle per evitare qualsiasi composizione sul lato server.</span><span class="sxs-lookup"><span data-stu-id="c78a6-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="c78a6-302">I metodi FromSql possono essere specificati solo in radici di query</span><span class="sxs-lookup"><span data-stu-id="c78a6-302">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="c78a6-303">Problema n. 15704</span><span class="sxs-lookup"><span data-stu-id="c78a6-303">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="c78a6-304">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-304">**Old behavior**</span></span>

<span data-ttu-id="c78a6-305">Prima di EF Core 3.0, il metodo `FromSql` poteva essere specificato in un punto qualsiasi nella query.</span><span class="sxs-lookup"><span data-stu-id="c78a6-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="c78a6-306">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-306">**New behavior**</span></span>

<span data-ttu-id="c78a6-307">A partire da EF Core 3.0, i nuovi metodi `FromSqlRaw` e `FromSqlInterpolated` (che sostituiscono`FromSql`) possono essere specificati solo per radici di query, ad esempio direttamente in `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="c78a6-308">Qualsiasi tentativo di specificarli altrove causerà un errore di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="c78a6-309">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-309">**Why**</span></span>

<span data-ttu-id="c78a6-310">La specifica di `FromSql` in qualsiasi posizione diversa da un `DbSet` non ha alcun significato aggiuntivo oppure valore aggiunto e può causare ambiguità in determinati scenari.</span><span class="sxs-lookup"><span data-stu-id="c78a6-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="c78a6-311">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-311">**Mitigations**</span></span>

<span data-ttu-id="c78a6-312">Le chiamate di `FromSql` devono essere spostate in modo da comparire direttamente nel `DbSet` a cui si applicano.</span><span class="sxs-lookup"><span data-stu-id="c78a6-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="c78a6-313">Le query senza rilevamento delle modifiche non eseguono più la risoluzione delle identità</span><span class="sxs-lookup"><span data-stu-id="c78a6-313">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="c78a6-314">Problema n. 13518</span><span class="sxs-lookup"><span data-stu-id="c78a6-314">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="c78a6-315">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-315">**Old behavior**</span></span>

<span data-ttu-id="c78a6-316">Prima di EF Core 3.0, la stessa istanza di un'entità poteva essere usata per ogni occorrenza di un entità con tipo e ID specifici.</span><span class="sxs-lookup"><span data-stu-id="c78a6-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="c78a6-317">Questo comportamento corrisponde a quello delle query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="c78a6-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="c78a6-318">Ad esempio, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="c78a6-319">restituisce la stessa istanza di `Category` per ogni `Product` associato alla categoria specificata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="c78a6-320">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-320">**New behavior**</span></span>

<span data-ttu-id="c78a6-321">A partire da EF Core 3.0, vengono create istanze di entità diverse quando un'entità con un determinato tipo e ID viene rilevata in posizioni diverse nel grafico restituito.</span><span class="sxs-lookup"><span data-stu-id="c78a6-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="c78a6-322">La query precedente, ad esempio, ora restituirà una nuova istanza di `Category` per ogni `Product` anche quando due prodotti sono associati alla stessa categoria.</span><span class="sxs-lookup"><span data-stu-id="c78a6-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="c78a6-323">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-323">**Why**</span></span>

<span data-ttu-id="c78a6-324">La risoluzione delle identità (ovvero il processo per determinare che un'entità ha lo stesso tipo e ID di un'entità rilevata in precedenza) aggiunge un ulteriore sovraccarico della memoria e delle prestazioni,</span><span class="sxs-lookup"><span data-stu-id="c78a6-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="c78a6-325">il che va ad annullare il vantaggio derivante dall'uso delle query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="c78a6-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="c78a6-326">Inoltre, anche se in alcuni casi la risoluzione delle identità può essere utile, tuttavia non è necessaria se le entità devono essere serializzate e inviate a un client, come avviene comunemente con le query senza rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="c78a6-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="c78a6-327">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-327">**Mitigations**</span></span>

<span data-ttu-id="c78a6-328">Se la risoluzione delle identità è necessaria, usare una query con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="c78a6-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="c78a6-329">~~L'esecuzione di query viene registrata a livello di debug~~ - Modifica annullata</span><span class="sxs-lookup"><span data-stu-id="c78a6-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="c78a6-330">Problema n. 14523</span><span class="sxs-lookup"><span data-stu-id="c78a6-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="c78a6-331">Questa modifica è stata annullata perché la nuova configurazione in EF Core 3.0 consente all'applicazione di specificare il livello di log per qualsiasi evento.</span><span class="sxs-lookup"><span data-stu-id="c78a6-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="c78a6-332">Ad esempio, per impostare la registrazione di SQL sul livello `Debug`, configurare il livello in modo esplicito in `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="c78a6-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="c78a6-333">I valori di chiave temporanei non sono più impostati nelle istanze di entità</span><span class="sxs-lookup"><span data-stu-id="c78a6-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="c78a6-334">Problema n. 12378</span><span class="sxs-lookup"><span data-stu-id="c78a6-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="c78a6-335">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-335">**Old behavior**</span></span>

<span data-ttu-id="c78a6-336">Nelle versioni precedenti a EF Core 3.0 i valori temporanei venivano assegnati a tutte le proprietà di chiave per cui veniva in seguito generato un valore reale dal database.</span><span class="sxs-lookup"><span data-stu-id="c78a6-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="c78a6-337">In genere questi valori temporanei erano numeri negativi elevati.</span><span class="sxs-lookup"><span data-stu-id="c78a6-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="c78a6-338">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-338">**New behavior**</span></span>

<span data-ttu-id="c78a6-339">A partire dalla versione 3.0, EF Core archivia il valore di chiave temporaneo come parte delle informazioni di rilevamento dell'entità e non modifica la proprietà di chiave.</span><span class="sxs-lookup"><span data-stu-id="c78a6-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="c78a6-340">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-340">**Why**</span></span>

<span data-ttu-id="c78a6-341">Questa modifica è stata apportata per impedire che i valori di chiave temporanei diventino erroneamente permanenti quando un'entità rilevata in precedenza da un'istanza `DbContext` viene spostata in un'altra istanza `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="c78a6-342">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-342">**Mitigations**</span></span>

<span data-ttu-id="c78a6-343">Le applicazioni che assegnano valori di chiave primaria in chiavi esterne per creare associazioni tra le entità possono dipendere dal comportamento precedente se le chiavi primarie vengono generate dall'archivio e appartengono a entità con stato `Added`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="c78a6-344">Questo può essere evitato:</span><span class="sxs-lookup"><span data-stu-id="c78a6-344">This can be avoided by:</span></span>
* <span data-ttu-id="c78a6-345">Non usando chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="c78a6-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="c78a6-346">Impostando le proprietà di navigazione in modo da creare relazioni anziché impostando valori di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="c78a6-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="c78a6-347">Ottenendo i valori di chiave temporanei effettivi dalle informazioni di rilevamento dell'entità.</span><span class="sxs-lookup"><span data-stu-id="c78a6-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="c78a6-348">Ad esempio, `context.Entry(blog).Property(e => e.Id).CurrentValue` restituisce il valore temporaneo anche quando `blog.Id` non è stato impostato.</span><span class="sxs-lookup"><span data-stu-id="c78a6-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="c78a6-349">DetectChanges rispetta i valori di chiave generati dall'archivio</span><span class="sxs-lookup"><span data-stu-id="c78a6-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="c78a6-350">Problema n. 14616</span><span class="sxs-lookup"><span data-stu-id="c78a6-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="c78a6-351">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-351">**Old behavior**</span></span>

<span data-ttu-id="c78a6-352">Nelle versioni precedenti a EF Core 3.0 un'entità non rilevata individuata da `DetectChanges` veniva rilevata nello stato `Added` e inserita come nuova riga quando veniva eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="c78a6-353">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-353">**New behavior**</span></span>

<span data-ttu-id="c78a6-354">A partire da EF Core 3.0, se un'entità usa valori di chiave generati e viene impostato un valore di chiave, l'entità viene rilevata nello stato `Modified`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="c78a6-355">Ciò significa che si presuppone l'esistenza di una riga per l'entità che viene aggiornata quando viene eseguita una chiamata a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="c78a6-356">Se il valore di chiave non viene impostato o se il tipo di entità non usa chiavi generate, la nuova entità viene rilevata come `Added` come nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="c78a6-357">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-357">**Why**</span></span>

<span data-ttu-id="c78a6-358">Questa modifica è stata apportata per rendere più semplice e coerente l'uso di grafici di entità disconnesse con chiavi generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="c78a6-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="c78a6-359">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-359">**Mitigations**</span></span>

<span data-ttu-id="c78a6-360">Questa modifica può interrompere un'applicazione se un tipo di entità è configurato per l'uso di chiavi generate ma i valori di chiave sono impostati in modo esplicito per le nuove istanze.</span><span class="sxs-lookup"><span data-stu-id="c78a6-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="c78a6-361">La correzione consiste nel configurare in modo esplicito le proprietà di chiave per non usare valori generati.</span><span class="sxs-lookup"><span data-stu-id="c78a6-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="c78a6-362">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="c78a6-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="c78a6-363">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="c78a6-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="c78a6-364">Le eliminazioni a catena vengono ora eseguite immediatamente per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="c78a6-365">Problema n. 10114</span><span class="sxs-lookup"><span data-stu-id="c78a6-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="c78a6-366">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-366">**Old behavior**</span></span>

<span data-ttu-id="c78a6-367">Nelle versioni precedenti alla versione 3.0, EF Core applicava azioni a catena (eliminazione delle entità dipendenti quando veniva eliminata un'entità di sicurezza obbligatoria o veniva recisa la relazione con un'entità di sicurezza obbligatoria) solo dopo la chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="c78a6-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="c78a6-368">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-368">**New behavior**</span></span>

<span data-ttu-id="c78a6-369">A partire dalla versione 3.0, EF Core applica le azioni a catena non appena viene rilevata la condizione di attivazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="c78a6-370">Ad esempio, la chiamata a `context.Remove()` per eliminare un'entità di sicurezza causa anche l'impostazione immediata di tutti i dipendenti obbligatori correlati rilevati su `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="c78a6-371">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-371">**Why**</span></span>

<span data-ttu-id="c78a6-372">Questa modifica è stata apportata per migliorare l'esperienza di associazione di dati e degli scenari di controllo in cui è importante individuare le entità che verranno eliminate _prima della chiamata a _ `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="c78a6-373">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-373">**Mitigations**</span></span>

<span data-ttu-id="c78a6-374">Il comportamento precedente può essere ripristinato tramite le impostazioni in `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="c78a6-375">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="c78a6-376">Il caricamento eager delle entità correlate ora si verifica in una singola query</span><span class="sxs-lookup"><span data-stu-id="c78a6-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="c78a6-377">Rilevamento del problema #18022</span><span class="sxs-lookup"><span data-stu-id="c78a6-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="c78a6-378">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-378">**Old behavior**</span></span>

<span data-ttu-id="c78a6-379">Prima del 3,0, il caricamento con entusiasmo degli spostamenti delle raccolte tramite `Include` gli operatori causava la generazione di più query nel database relazionale, una per ogni tipo di entità correlato.</span><span class="sxs-lookup"><span data-stu-id="c78a6-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="c78a6-380">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-380">**New behavior**</span></span>

<span data-ttu-id="c78a6-381">A partire da 3,0, EF Core genera una singola query con JOIN nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="c78a6-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="c78a6-382">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-382">**Why**</span></span>

<span data-ttu-id="c78a6-383">L'invio di più query per l'implementazione di una singola query LINQ ha causato numerosi problemi, incluse le prestazioni negative in quanto sono stati necessari più round trip di database e i dati coerenza problemi poiché ogni query poteva osservare uno stato diverso del database.</span><span class="sxs-lookup"><span data-stu-id="c78a6-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="c78a6-384">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-384">**Mitigations**</span></span>

<span data-ttu-id="c78a6-385">Sebbene tecnicamente non si tratti di una modifica di rilievo, potrebbe avere un impatto significativo sulle prestazioni dell'applicazione quando una singola query contiene un numero elevato di operatori per le esplorazioni della `Include` raccolta.</span><span class="sxs-lookup"><span data-stu-id="c78a6-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="c78a6-386">Per ulteriori informazioni e per riscrivere le query in modo più efficiente, [vedere il commento](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="c78a6-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="c78a6-387">Semantica più chiara per DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="c78a6-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="c78a6-388">Problema n. 12661</span><span class="sxs-lookup"><span data-stu-id="c78a6-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="c78a6-389">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-389">**Old behavior**</span></span>

<span data-ttu-id="c78a6-390">Prima della versione 3.0, `DeleteBehavior.Restrict` creava chiavi esterne nel database con la semantica `Restrict`, ma modificava anche la correzione interna in modo non ovvio.</span><span class="sxs-lookup"><span data-stu-id="c78a6-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="c78a6-391">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-391">**New behavior**</span></span>

<span data-ttu-id="c78a6-392">A partire dalla versione 3.0, `DeleteBehavior.Restrict` assicura che le chiavi esterne vengano create con la semantica `Restrict`, ovvero non a cascata e con generazione di un'eccezione in caso di violazione di vincolo, senza influire sulla correzione interna di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="c78a6-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="c78a6-393">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-393">**Why**</span></span>

<span data-ttu-id="c78a6-394">Questa modifica è stata apportata per migliorare l'esperienza di uso di `DeleteBehavior` in modo intuitivo, senza effetti collaterali imprevisti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="c78a6-395">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-395">**Mitigations**</span></span>

<span data-ttu-id="c78a6-396">Il comportamento precedente può essere ripristinato tramite `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="c78a6-397">I tipi di query vengono consolidati con tipi di entità</span><span class="sxs-lookup"><span data-stu-id="c78a6-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="c78a6-398">Problema n. 14194</span><span class="sxs-lookup"><span data-stu-id="c78a6-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="c78a6-399">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-399">**Old behavior**</span></span>

<span data-ttu-id="c78a6-400">Nelle versioni precedenti a EF Core 3.0 i [tipi di query](xref:core/modeling/keyless-entity-types) erano uno strumento per eseguire query su dati che non definiscono una chiave primaria in modo strutturato.</span><span class="sxs-lookup"><span data-stu-id="c78a6-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="c78a6-401">Veniva infatti usato un tipo di query per eseguire il mapping di tipi di entità senza chiavi (più probabilmente da una vista, ma anche da una tabella), mentre veniva usato un tipo di entità normale quando era disponibile una chiave (più probabilmente da una tabella, ma anche da una vista).</span><span class="sxs-lookup"><span data-stu-id="c78a6-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="c78a6-402">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-402">**New behavior**</span></span>

<span data-ttu-id="c78a6-403">Un tipo di query diventa ora semplicemente un tipo di entità senza chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="c78a6-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="c78a6-404">I tipi di entità senza chiave hanno la stessa funzionalità dei tipi di query nelle versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="c78a6-405">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-405">**Why**</span></span>

<span data-ttu-id="c78a6-406">Questa modifica è stata apportata per ridurre la confusione riguardo lo scopo dei tipi di query.</span><span class="sxs-lookup"><span data-stu-id="c78a6-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="c78a6-407">In particolare, si tratta di tipi di entità senza chiave che sono intrinsecamente di sola lettura per questo motivo ma non dovrebbero essere usati solo perché un tipo di entità deve essere di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="c78a6-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="c78a6-408">Analogamente, spesso vengono mappati alle viste solo perché le viste spesso non definiscono le chiavi.</span><span class="sxs-lookup"><span data-stu-id="c78a6-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="c78a6-409">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-409">**Mitigations**</span></span>

<span data-ttu-id="c78a6-410">Le parti dell'API seguenti sono ora obsolete:</span><span class="sxs-lookup"><span data-stu-id="c78a6-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="c78a6-411">**`ModelBuilder.Query<>()`** -È invece necessario `ModelBuilder.Entity<>().HasNoKey()` chiamare il metodo per contrassegnare un tipo di entità senza chiavi.</span><span class="sxs-lookup"><span data-stu-id="c78a6-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="c78a6-412">Non ne viene eseguita la configurazione per convenzione per evitare una configurazione errata quando è prevista una chiave primaria che tuttavia non corrisponde alla convenzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="c78a6-413">**`DbQuery<>`** - `DbSet<>` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="c78a6-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="c78a6-414">**`DbContext.Query<>()`** - `DbContext.Set<>()` È invece consigliabile usare.</span><span class="sxs-lookup"><span data-stu-id="c78a6-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="c78a6-415">**`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` Usare invece \* \*.</span><span class="sxs-lookup"><span data-stu-id="c78a6-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="c78a6-416">A causa di [un problema in 3. x](https://github.com/dotnet/efcore/issues/19537) durante l'esecuzione di query su entità senza chiave che dispongono di tutte le proprietà impostate su `null` un `null` verrà restituito al posto di un'entità, se il problema è applicabile anche allo scenario, aggiungere la logica per gestire `null` i risultati.</span><span class="sxs-lookup"><span data-stu-id="c78a6-416">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="c78a6-417">L'API di configurazione per le relazioni di tipo di proprietà è stata modificata</span><span class="sxs-lookup"><span data-stu-id="c78a6-417">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="c78a6-418">[Rilevamento del problema #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444) 
 [Rilevamento del problema #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148) 
 [Rilevamento del problema #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="c78a6-418">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="c78a6-419">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-419">**Old behavior**</span></span>

<span data-ttu-id="c78a6-420">Nelle versioni precedenti a EF Core 3.0 la configurazione della relazione di proprietà veniva eseguita direttamente dopo la chiamata a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-420">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="c78a6-421">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-421">**New behavior**</span></span>

<span data-ttu-id="c78a6-422">A partire da EF Core 3.0, è disponibile l'API Fluent per configurare una proprietà di navigazione per il proprietario usando `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-422">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="c78a6-423">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-423">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="c78a6-424">La configurazione correlata alla relazione tra proprietario e elemento di proprietà deve essere ora concatenata dopo `WithOwner()` in modo analogo a come vengono configurate altre relazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-424">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="c78a6-425">La configurazione per il tipo di proprietà viene comunque concatenata dopo `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-425">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="c78a6-426">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-426">For example:</span></span>

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

<span data-ttu-id="c78a6-427">Inoltre, la chiamata a `Entity()`, `HasOne()` o `Set()` con una destinazione di tipo di proprietà genera ora un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-427">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="c78a6-428">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-428">**Why**</span></span>

<span data-ttu-id="c78a6-429">Questa modifica è stata apportata per creare una separazione più netta tra la configurazione del tipo di proprietà e la _relazione_ con il tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-429">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="c78a6-430">Ciò consente di eliminare ambiguità e confusione su metodi come `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-430">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="c78a6-431">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-431">**Mitigations**</span></span>

<span data-ttu-id="c78a6-432">Modificare la configurazione delle relazioni dei tipi di proprietà per usare la superficie della nuova API come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-432">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="c78a6-433">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="c78a6-433">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="c78a6-434">Problema n. 9005</span><span class="sxs-lookup"><span data-stu-id="c78a6-434">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="c78a6-435">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-435">**Old behavior**</span></span>

<span data-ttu-id="c78a6-436">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-436">Consider the following model:</span></span>
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
<span data-ttu-id="c78a6-437">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o viene mappato in modo esplicito alla stessa tabella, era sempre necessaria un'istanza di `OrderDetails` per l'aggiunta di un nuovo `Order`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-437">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="c78a6-438">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-438">**New behavior**</span></span>

<span data-ttu-id="c78a6-439">A partire dalla versione 3.0, EF Core consente di aggiungere un `Order` senza un `OrderDetails` ed esegue il mapping di tutte le proprietà di `OrderDetails`, tranne che la chiave primaria, a colonne che ammettono valori Null.</span><span class="sxs-lookup"><span data-stu-id="c78a6-439">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="c78a6-440">In fase di query, EF Core imposta `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-440">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="c78a6-441">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-441">**Mitigations**</span></span>

<span data-ttu-id="c78a6-442">Se il modello include una tabella condivisa dipendente con tutte le colonne facoltative, ma è previsto che la navigazione che punta a essa non sia `null`, l'applicazione deve essere modificata per gestire casi in cui la navigazione è `null`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-442">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="c78a6-443">Se questo non è possibile, è consigliabile aggiungere una proprietà obbligatoria al tipo di entità o assegnare un valore non `null` ad almeno una proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-443">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="c78a6-444">Tutte le entità che condividono una tabella con una colonna di token di concorrenza devono eseguirne il mapping a una proprietà</span><span class="sxs-lookup"><span data-stu-id="c78a6-444">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="c78a6-445">Problema n. 14154</span><span class="sxs-lookup"><span data-stu-id="c78a6-445">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="c78a6-446">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-446">**Old behavior**</span></span>

<span data-ttu-id="c78a6-447">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-447">Consider the following model:</span></span>
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
<span data-ttu-id="c78a6-448">Prima di EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o è mappato in modo esplicito alla stessa tabella, il solo aggiornamento di `OrderDetails` non aggiornerà il valore `Version` nel client e l'aggiornamento successivo avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-448">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="c78a6-449">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-449">**New behavior**</span></span>

<span data-ttu-id="c78a6-450">A partire dalla versione 3.0, EF Core propaga il nuovo valore `Version` a `Order` se è proprietario di `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-450">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="c78a6-451">In caso contrario, viene generata un'eccezione durante la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="c78a6-451">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="c78a6-452">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-452">**Why**</span></span>

<span data-ttu-id="c78a6-453">Questa modifica è stata apportata per evitare un valore del token di concorrenza non aggiornato quando viene aggiornata solo una delle entità mappate alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="c78a6-453">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="c78a6-454">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-454">**Mitigations**</span></span>

<span data-ttu-id="c78a6-455">Tutte le entità che condividono la tabella devono includere una proprietà mappata alla colonna del token di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-455">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="c78a6-456">È possibile crearne una in stato shadow:</span><span class="sxs-lookup"><span data-stu-id="c78a6-456">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="c78a6-457">Non è possibile eseguire query sulle entità di proprietà senza il proprietario utilizzando una query di rilevamento</span><span class="sxs-lookup"><span data-stu-id="c78a6-457">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="c78a6-458">Rilevamento del problema #18876</span><span class="sxs-lookup"><span data-stu-id="c78a6-458">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="c78a6-459">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-459">**Old behavior**</span></span>

<span data-ttu-id="c78a6-460">Prima di EF Core 3,0, le entità di proprietà potevano essere sottoposte a query come qualsiasi altra navigazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-460">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="c78a6-461">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-461">**New behavior**</span></span>

<span data-ttu-id="c78a6-462">A partire da 3,0, EF Core genererà un'operazione se una query di rilevamento proietta un'entità di proprietà senza proprietario.</span><span class="sxs-lookup"><span data-stu-id="c78a6-462">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="c78a6-463">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-463">**Why**</span></span>

<span data-ttu-id="c78a6-464">Le entità di proprietà non possono essere modificate senza il proprietario, quindi nella maggior parte dei casi l'esecuzione di query in questo modo è un errore.</span><span class="sxs-lookup"><span data-stu-id="c78a6-464">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="c78a6-465">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-465">**Mitigations**</span></span>

<span data-ttu-id="c78a6-466">Se l'entità di proprietà deve essere rilevata in modo da essere modificata in un secondo momento, il proprietario deve essere incluso nella query.</span><span class="sxs-lookup"><span data-stu-id="c78a6-466">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="c78a6-467">In caso contrario `AsNoTracking()` , aggiungere una chiamata:</span><span class="sxs-lookup"><span data-stu-id="c78a6-467">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="c78a6-468">Per le proprietà ereditate da tipi senza mapping viene ora eseguito il mapping a una singola colonna per tutti i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="c78a6-468">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="c78a6-469">Problema n. 13998</span><span class="sxs-lookup"><span data-stu-id="c78a6-469">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="c78a6-470">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-470">**Old behavior**</span></span>

<span data-ttu-id="c78a6-471">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-471">Consider the following model:</span></span>
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

<span data-ttu-id="c78a6-472">Prima di EF Core 3.0, per la proprietà `ShippingAddress` sarebbe stato eseguito il mapping a colonne separate per `BulkOrder` e `Order` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="c78a6-472">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="c78a6-473">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-473">**New behavior**</span></span>

<span data-ttu-id="c78a6-474">A partire dalla versione 3.0, EF Core crea solo una colonna per `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-474">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="c78a6-475">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-475">**Why**</span></span>

<span data-ttu-id="c78a6-476">Il comportamento precedente non era previsto.</span><span class="sxs-lookup"><span data-stu-id="c78a6-476">The old behavoir was unexpected.</span></span>

<span data-ttu-id="c78a6-477">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-477">**Mitigations**</span></span>

<span data-ttu-id="c78a6-478">È ancora possibile eseguire il mapping esplicito della proprietà a una colonna separata per i tipi derivati:</span><span class="sxs-lookup"><span data-stu-id="c78a6-478">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="c78a6-479">La convenzione di proprietà di chiave esterna non ha più lo stesso nome della proprietà dell'entità di sicurezza</span><span class="sxs-lookup"><span data-stu-id="c78a6-479">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="c78a6-480">Problema n. 13274</span><span class="sxs-lookup"><span data-stu-id="c78a6-480">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="c78a6-481">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-481">**Old behavior**</span></span>

<span data-ttu-id="c78a6-482">Si consideri il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-482">Consider the following model:</span></span>
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
<span data-ttu-id="c78a6-483">Nelle versioni precedenti a EF Core 3.0 veniva usata la proprietà `CustomerId` per la chiave esterna per convenzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-483">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="c78a6-484">Tuttavia, se `Order` è un tipo di proprietà, `CustomerId` sarebbe la chiave primaria e ciò non è in genere auspicabile.</span><span class="sxs-lookup"><span data-stu-id="c78a6-484">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="c78a6-485">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-485">**New behavior**</span></span>

<span data-ttu-id="c78a6-486">A partire da 3.0, EF Core non tenta di usare le proprietà per le chiavi esterne per convenzione se hanno lo stesso nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-486">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="c78a6-487">Viene ancora eseguita la corrispondenza tra i criteri del nome del tipo dell'entità di sicurezza concatenato al nome della proprietà dell'entità di sicurezza e il nome di navigazione concatenato al nome della proprietà dell'entità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-487">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="c78a6-488">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-488">For example:</span></span>

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

<span data-ttu-id="c78a6-489">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-489">**Why**</span></span>

<span data-ttu-id="c78a6-490">Questa modifica è stata apportata per evitare una definizione errata della proprietà di chiave primaria nel tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-490">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="c78a6-491">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-491">**Mitigations**</span></span>

<span data-ttu-id="c78a6-492">Se la proprietà è stata progettata per essere la chiave esterna e di conseguenza parte della chiave primaria, configurarla in modo esplicito come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="c78a6-492">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="c78a6-493">La connessione al database viene ora chiusa se non viene più usata prima del completamento di TransactionScope</span><span class="sxs-lookup"><span data-stu-id="c78a6-493">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="c78a6-494">Problema n. 14218</span><span class="sxs-lookup"><span data-stu-id="c78a6-494">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="c78a6-495">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-495">**Old behavior**</span></span>

<span data-ttu-id="c78a6-496">Prima di EF Core 3.0, se il contesto apre la connessione all'interno di un `TransactionScope`, la connessione rimane aperta mentre è attivo il `TransactionScope` corrente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-496">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="c78a6-497">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-497">**New behavior**</span></span>

<span data-ttu-id="c78a6-498">A partire dalla versione 3.0, EF Core chiude la connessione non appena non viene più usata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-498">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="c78a6-499">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-499">**Why**</span></span>

<span data-ttu-id="c78a6-500">Questa modifica consente di usare più contesti nello stesso `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-500">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="c78a6-501">Il nuovo comportamento corrisponde anche a EF6.</span><span class="sxs-lookup"><span data-stu-id="c78a6-501">The new behavior also matches EF6.</span></span>

<span data-ttu-id="c78a6-502">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-502">**Mitigations**</span></span>

<span data-ttu-id="c78a6-503">Se la connessione deve rimanere aperta, la chiamata esplicita di `OpenConnection()` garantirà che EF Core non la chiuda prematuramente:</span><span class="sxs-lookup"><span data-stu-id="c78a6-503">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="c78a6-504">Ogni proprietà usa la generazione di chiavi di tipo intero in memoria indipendenti</span><span class="sxs-lookup"><span data-stu-id="c78a6-504">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="c78a6-505">Problema n. 6872</span><span class="sxs-lookup"><span data-stu-id="c78a6-505">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="c78a6-506">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-506">**Old behavior**</span></span>

<span data-ttu-id="c78a6-507">Nelle versioni precedenti a EF Core 3.0 veniva usato un unico generatore di valori condiviso per tutte le proprietà di chiavi di tipo intero in memoria.</span><span class="sxs-lookup"><span data-stu-id="c78a6-507">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="c78a6-508">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-508">**New behavior**</span></span>

<span data-ttu-id="c78a6-509">A partire da EF Core 3.0, ogni proprietà di chiave di tipo intero riceve un generatore di valori quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="c78a6-509">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="c78a6-510">Inoltre, se il database viene eliminato, la generazione di chiavi viene reimpostata per tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="c78a6-510">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="c78a6-511">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-511">**Why**</span></span>

<span data-ttu-id="c78a6-512">Questa modifica è stata apportata per allineare maggiormente la generazione di chiavi in memoria alla generazione di chiavi del database reale e per migliorare la possibilità di isolare i test l'uno dall'altro quando viene usato il database in memoria.</span><span class="sxs-lookup"><span data-stu-id="c78a6-512">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="c78a6-513">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-513">**Mitigations**</span></span>

<span data-ttu-id="c78a6-514">Ciò può interrompere un'applicazione che si basa sull'impostazione di valori di chiave in memoria specifici.</span><span class="sxs-lookup"><span data-stu-id="c78a6-514">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="c78a6-515">È consigliabile non basare l'applicazione su valori di chiave specifici o eseguire l'aggiornamento per passare al nuovo comportamento.</span><span class="sxs-lookup"><span data-stu-id="c78a6-515">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="c78a6-516">I campi sottostanti vengono usati per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-516">Backing fields are used by default</span></span>

[<span data-ttu-id="c78a6-517">Problema n. 12430</span><span class="sxs-lookup"><span data-stu-id="c78a6-517">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="c78a6-518">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-518">**Old behavior**</span></span>

<span data-ttu-id="c78a6-519">Nelle versioni precedenti alla versione 3.0, anche se il campo sottostante di una proprietà era noto, per impostazione predefinita EF Core eseguiva la lettura e la scrittura del valore della proprietà usando i metodi getter e setter della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-519">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="c78a6-520">L'eccezione era costituita dall'esecuzione di query in cui il campo sottostante, se noto, veniva impostato direttamente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-520">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="c78a6-521">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-521">**New behavior**</span></span>

<span data-ttu-id="c78a6-522">A partire da EF Core 3.0, se il campo sottostante di una proprietà è noto, la lettura e la scrittura della proprietà vengono sempre eseguite usando il campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="c78a6-522">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="c78a6-523">Ciò potrebbe causare un'interruzione dell'applicazione se l'applicazione si basa su un comportamento aggiuntivo codificato nei metodi getter o setter.</span><span class="sxs-lookup"><span data-stu-id="c78a6-523">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="c78a6-524">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-524">**Why**</span></span>

<span data-ttu-id="c78a6-525">Questa modifica è stata apportata per impedire a EF Core di attivare per errore la logica di business per impostazione predefinita quando si eseguono operazioni di database che interessano le entità.</span><span class="sxs-lookup"><span data-stu-id="c78a6-525">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="c78a6-526">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-526">**Mitigations**</span></span>

<span data-ttu-id="c78a6-527">È possibile ripristinare il comportamento delle versioni precedenti alla versione 3.0 tramite la configurazione della modalità di accesso delle proprietà in `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-527">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="c78a6-528">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-528">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="c78a6-529">Viene generata un'eccezione se vengono trovati più campi sottostanti compatibili</span><span class="sxs-lookup"><span data-stu-id="c78a6-529">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="c78a6-530">Problema n. 12523</span><span class="sxs-lookup"><span data-stu-id="c78a6-530">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="c78a6-531">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-531">**Old behavior**</span></span>

<span data-ttu-id="c78a6-532">Nelle versioni precedenti a EF Core 3.0, se più campi soddisfacevano le regole di ricerca del campo sottostante di una proprietà, veniva selezionato un solo campo in base a un ordine di precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-532">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="c78a6-533">Ciò poteva causare l'uso di un campo non corretto nei casi ambigui.</span><span class="sxs-lookup"><span data-stu-id="c78a6-533">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="c78a6-534">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-534">**New behavior**</span></span>

<span data-ttu-id="c78a6-535">A partire da EF Core 3.0, se più campi corrispondono alla stessa proprietà, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-535">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="c78a6-536">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-536">**Why**</span></span>

<span data-ttu-id="c78a6-537">Questa modifica è stata apportata per evitare di usare automaticamente un campo rispetto a un altro quando un solo campo può essere quello corretto.</span><span class="sxs-lookup"><span data-stu-id="c78a6-537">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="c78a6-538">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-538">**Mitigations**</span></span>

<span data-ttu-id="c78a6-539">Per le proprietà con campi sottostanti ambigui, il campo da usare deve essere specificato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="c78a6-539">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="c78a6-540">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="c78a6-540">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="c78a6-541">I nomi delle proprietà solo campo devono corrispondere al nome di campo</span><span class="sxs-lookup"><span data-stu-id="c78a6-541">Field-only property names should match the field name</span></span>

<span data-ttu-id="c78a6-542">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-542">**Old behavior**</span></span>

<span data-ttu-id="c78a6-543">Prima di EF Core 3,0, una proprietà può essere specificata da un valore stringa e, se non è stata trovata alcuna proprietà con tale nome nel tipo .NET, EF Core tenterà di associarla a un campo usando le regole di convenzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-543">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

<span data-ttu-id="c78a6-544">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-544">**New behavior**</span></span>

<span data-ttu-id="c78a6-545">A partire da EF Core 3.0, una proprietà solo campo deve corrispondere esattamente al nome del campo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-545">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="c78a6-546">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-546">**Why**</span></span>

<span data-ttu-id="c78a6-547">Questa modifica è stata introdotta per evitare di usare lo stesso campo per due proprietà con nome simile. Le regole di corrispondenza per le proprietà solo campo sono state anche uniformate a quelle per le proprietà mappate a proprietà CLR.</span><span class="sxs-lookup"><span data-stu-id="c78a6-547">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="c78a6-548">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-548">**Mitigations**</span></span>

<span data-ttu-id="c78a6-549">Le proprietà solo campo devono avere lo stesso nome del campo a cui vengono mappate.</span><span class="sxs-lookup"><span data-stu-id="c78a6-549">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="c78a6-550">In una versione futura di EF Core dopo il 3,0, si prevede di abilitare di nuovo in modo esplicito il nome di un campo diverso dal nome della proprietà (vedere il problema [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="c78a6-550">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="c78a6-551">AddDbContext/AddDbContextPool non chiamano più AddLogging e AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="c78a6-551">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="c78a6-552">Problema n. 14756</span><span class="sxs-lookup"><span data-stu-id="c78a6-552">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="c78a6-553">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-553">**Old behavior**</span></span>

<span data-ttu-id="c78a6-554">Prima di EF Core 3,0, chiamando `AddDbContext` o `AddDbContextPool` sarebbero stati registrati anche i servizi di memorizzazione nella cache e di registrazione della memoria con le chiamate a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) e [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="c78a6-554">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="c78a6-555">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-555">**New behavior**</span></span>

<span data-ttu-id="c78a6-556">A partire da EF Core 3.0, `AddDbContext` e `AddDbContextPool` non registreranno più questi servizi con inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="c78a6-556">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="c78a6-557">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-557">**Why**</span></span>

<span data-ttu-id="c78a6-558">EF Core 3.0 non richiede che questi servizi siano inclusi nel contenitore di inserimento delle dipendenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-558">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="c78a6-559">Tuttavia, se `ILoggerFactory` è registrato nel contenitore di inserimento delle dipendenze dell'applicazione, verrà ancora usato da EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-559">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="c78a6-560">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-560">**Mitigations**</span></span>

<span data-ttu-id="c78a6-561">Se l'applicazione necessita di questi servizi, registrarli in modo esplicito con il contenitore di inserimento delle dipendenze usando [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="c78a6-561">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="c78a6-562">AddEntityFramework \* aggiunge IMemoryCache con un limite di dimensioni</span><span class="sxs-lookup"><span data-stu-id="c78a6-562">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="c78a6-563">Rilevamento del problema #12905</span><span class="sxs-lookup"><span data-stu-id="c78a6-563">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="c78a6-564">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-564">**Old behavior**</span></span>

<span data-ttu-id="c78a6-565">Prima di EF Core 3,0, i metodi di chiamata `AddEntityFramework*` registravano anche i servizi di memorizzazione nella cache di memoria con di senza un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-565">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="c78a6-566">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-566">**New behavior**</span></span>

<span data-ttu-id="c78a6-567">A partire da EF Core 3,0, registrerà `AddEntityFramework*` un servizio IMemoryCache con un limite di dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-567">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="c78a6-568">Se altri servizi aggiunti successivamente dipendono da IMemoryCache, è possibile raggiungere rapidamente il limite predefinito che causa eccezioni o prestazioni ridotte.</span><span class="sxs-lookup"><span data-stu-id="c78a6-568">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="c78a6-569">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-569">**Why**</span></span>

<span data-ttu-id="c78a6-570">L'utilizzo di IMemoryCache senza un limite può comportare l'utilizzo di memoria non controllata se è presente un bug nella logica di memorizzazione nella cache delle query o se le query vengono generate in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="c78a6-570">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="c78a6-571">La presenza di un limite predefinito attenua un potenziale attacco DoS.</span><span class="sxs-lookup"><span data-stu-id="c78a6-571">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="c78a6-572">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-572">**Mitigations**</span></span>

<span data-ttu-id="c78a6-573">Nella maggior parte dei casi, la chiamata a `AddEntityFramework*` non è necessaria se `AddDbContext` `AddDbContextPool` viene chiamato anche o.</span><span class="sxs-lookup"><span data-stu-id="c78a6-573">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="c78a6-574">Pertanto, la migliore mitigazione consiste nel rimuovere la `AddEntityFramework*` chiamata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-574">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="c78a6-575">Se l'applicazione necessita di questi servizi, registrare un'implementazione di IMemoryCache in modo esplicito con il contenitore DI inserimento in anticipo usando [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="c78a6-575">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="c78a6-576">DbContext.Entry esegue ora un DetectChanges locale</span><span class="sxs-lookup"><span data-stu-id="c78a6-576">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="c78a6-577">Problema n. 13552</span><span class="sxs-lookup"><span data-stu-id="c78a6-577">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="c78a6-578">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-578">**Old behavior**</span></span>

<span data-ttu-id="c78a6-579">Nelle versioni precedenti a EF Core 3.0 la chiamata a `DbContext.Entry` causava il rilevamento delle modifiche per tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="c78a6-579">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="c78a6-580">Ciò garantiva l'aggiornamento dello stato esposto in `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-580">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="c78a6-581">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-581">**New behavior**</span></span>

<span data-ttu-id="c78a6-582">A partire da EF Core 3.0, la chiamata a `DbContext.Entry` causa ora solo il tentativo di rilevare le modifiche nell'entità specificata e in tutte le relative entità di sicurezza rilevate.</span><span class="sxs-lookup"><span data-stu-id="c78a6-582">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="c78a6-583">Ciò significa che le modifiche apportate altrove potrebbero non essere state rilevate tramite la chiamata al metodo e ciò potrebbe avere implicazioni sullo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-583">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="c78a6-584">Si noti che se `ChangeTracker.AutoDetectChangesEnabled` è impostato su `false`, verrà disabilitato anche questo tipo di rilevamento delle modifiche locali.</span><span class="sxs-lookup"><span data-stu-id="c78a6-584">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="c78a6-585">Gli altri metodi che causano il rilevamento delle modifiche, ad esempio `ChangeTracker.Entries` e `SaveChanges`, causano ancora un `DetectChanges` completo di tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="c78a6-585">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="c78a6-586">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-586">**Why**</span></span>

<span data-ttu-id="c78a6-587">Questa modifica è stata apportata per migliorare le prestazioni predefinite dell'uso di `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-587">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="c78a6-588">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-588">**Mitigations**</span></span>

<span data-ttu-id="c78a6-589">Chiamare `ChangeTracker.DetectChanges()` in modo esplicito prima di chiamare `Entry` per garantire il comportamento precedente alla versione 3.0.</span><span class="sxs-lookup"><span data-stu-id="c78a6-589">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="c78a6-590">Le chiavi matrice di byte e di stringhe non vengono generate dal client per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-590">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="c78a6-591">Problema n. 14617</span><span class="sxs-lookup"><span data-stu-id="c78a6-591">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="c78a6-592">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-592">**Old behavior**</span></span>

<span data-ttu-id="c78a6-593">Nelle versioni precedenti a EF Core 3.0 le proprietà di chiave `string` e `byte[]` potevano essere usate senza impostare in modo esplicito un valore non Null.</span><span class="sxs-lookup"><span data-stu-id="c78a6-593">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="c78a6-594">In questi casi, il valore di chiave veniva generato nel client come GUID, serializzato in byte per `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-594">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="c78a6-595">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-595">**New behavior**</span></span>

<span data-ttu-id="c78a6-596">A partire da EF Core 3.0 viene generata un'eccezione che indica che non è stato impostato alcun valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="c78a6-596">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="c78a6-597">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-597">**Why**</span></span>

<span data-ttu-id="c78a6-598">Questa modifica è stata apportata poiché i valori `string`/`byte[]` generati dal client non risultano in genere utili e il comportamento predefinito rendeva complesso ragionare sui valori di chiave generati in un modo comune.</span><span class="sxs-lookup"><span data-stu-id="c78a6-598">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="c78a6-599">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-599">**Mitigations**</span></span>

<span data-ttu-id="c78a6-600">È possibile ripristinare il comportamento precedente alla versione 3.0 specificando in modo esplicito che le proprietà di chiave devono usare i valori generati se non viene impostato alcun altro valore non Null.</span><span class="sxs-lookup"><span data-stu-id="c78a6-600">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="c78a6-601">Ad esempio, con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="c78a6-601">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="c78a6-602">Oppure con annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="c78a6-602">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="c78a6-603">ILoggerFactory è ora un servizio con ambito</span><span class="sxs-lookup"><span data-stu-id="c78a6-603">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="c78a6-604">Problema n. 14698</span><span class="sxs-lookup"><span data-stu-id="c78a6-604">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="c78a6-605">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-605">**Old behavior**</span></span>

<span data-ttu-id="c78a6-606">Nelle versioni precedenti a EF Core 3.0 `ILoggerFactory` veniva registrato come servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="c78a6-606">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="c78a6-607">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-607">**New behavior**</span></span>

<span data-ttu-id="c78a6-608">A partire da EF Core 3.0, `ILoggerFactory` viene registrato come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="c78a6-608">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="c78a6-609">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-609">**Why**</span></span>

<span data-ttu-id="c78a6-610">Questa modifica è stata apportata per consentire l'associazione di un logger a un'istanza `DbContext` che abilita altre funzionalità e rimuove alcuni casi di comportamento anomalo, ad esempio un'esplosione dei provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-610">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="c78a6-611">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-611">**Mitigations**</span></span>

<span data-ttu-id="c78a6-612">Questa modifica non dovrebbe influire sul codice dell'applicazione a meno che non vengano registrati e usati servizi personalizzati nel provider di servizi interno di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-612">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="c78a6-613">Questo non è un comportamento comune.</span><span class="sxs-lookup"><span data-stu-id="c78a6-613">This isn't common.</span></span>
<span data-ttu-id="c78a6-614">In questi casi la maggior parte delle operazioni continuano a essere eseguite correttamente, ma qualsiasi servizio singleton dipendente da `ILoggerFactory` dovrà essere modificato per ottenere `ILoggerFactory` in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-614">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="c78a6-615">Se si verificano situazioni simili, inviare una segnalazione nello [strumento di gestione dei problemi in GitHub relativo a EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) per comunicare in che modo viene usato `ILoggerFactory` per consentirci di comprendere meglio come evitare ulteriori interruzioni in futuro.</span><span class="sxs-lookup"><span data-stu-id="c78a6-615">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="c78a6-616">I proxy di caricamento lazy non presuppongono più che le proprietà di navigazione vengano caricate completamente</span><span class="sxs-lookup"><span data-stu-id="c78a6-616">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="c78a6-617">Problema n. 12780</span><span class="sxs-lookup"><span data-stu-id="c78a6-617">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="c78a6-618">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-618">**Old behavior**</span></span>

<span data-ttu-id="c78a6-619">Nelle versioni precedenti a EF Core 3.0, quando `DbContext` veniva eliminato non esisteva alcun metodo per scoprire se una determinata proprietà di navigazione in un'entità ottenuta da un contesto specifico veniva caricata completamente o meno.</span><span class="sxs-lookup"><span data-stu-id="c78a6-619">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="c78a6-620">I proxy presupponevano invece che venisse caricata una navigazione di riferimento se era presente un valore non Null e che venisse caricata una navigazione di raccolta se era presente un valore.</span><span class="sxs-lookup"><span data-stu-id="c78a6-620">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="c78a6-621">In questi casi il tentativo di eseguire un caricamento lazy non avrebbe avuto alcun esito.</span><span class="sxs-lookup"><span data-stu-id="c78a6-621">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="c78a6-622">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-622">**New behavior**</span></span>

<span data-ttu-id="c78a6-623">A partire da Entity Framework Core 3.0, i proxy tengono traccia del caricamento o mancato caricamento di una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-623">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="c78a6-624">Ciò significa che il tentativo di accedere a una proprietà di navigazione caricata dopo l'eliminazione del contesto non avrà mai alcun esito, anche quando la navigazione caricata è vuota o ha valore Null.</span><span class="sxs-lookup"><span data-stu-id="c78a6-624">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="c78a6-625">Al contrario, il tentativo di accedere a una proprietà di navigazione non caricata genera un'eccezione se il contesto viene eliminato anche se la proprietà di navigazione è una raccolta non vuota.</span><span class="sxs-lookup"><span data-stu-id="c78a6-625">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="c78a6-626">Se si verifica questa situazione significa che il codice dell'applicazione sta tentando di usare il caricamento lazy in un momento non valido e l'applicazione deve essere modificata in modo da non eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-626">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="c78a6-627">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-627">**Why**</span></span>

<span data-ttu-id="c78a6-628">Questa modifica è stata apportata per rendere coerente e corretto il comportamento durante un tentativo di caricamento lazy in un'istanza `DbContext` eliminata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-628">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="c78a6-629">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-629">**Mitigations**</span></span>

<span data-ttu-id="c78a6-630">Aggiornare il codice dell'applicazione per fare in modo che non venga tentato il caricamento lazy con un contesto eliminato oppure specificare una configurazione in modo che non venga eseguita alcuna operazione come descritto nel messaggio di eccezione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-630">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="c78a6-631">La creazione di un numero eccessivo di provider di servizi interni è ora un errore per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="c78a6-631">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="c78a6-632">Problema n. 10236</span><span class="sxs-lookup"><span data-stu-id="c78a6-632">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="c78a6-633">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-633">**Old behavior**</span></span>

<span data-ttu-id="c78a6-634">Nelle versioni precedenti a EF Core 3.0 veniva registrato un avviso per le applicazioni che creavano un numero eccessivo di provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-634">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="c78a6-635">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-635">**New behavior**</span></span>

<span data-ttu-id="c78a6-636">A partire da EF Core 3.0, l'avviso viene considerato un errore e viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-636">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="c78a6-637">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-637">**Why**</span></span>

<span data-ttu-id="c78a6-638">Questa modifica è stata apportata per gestire meglio il codice dell'applicazione tramite un'esposizione più esplicita di questa situazione di errore.</span><span class="sxs-lookup"><span data-stu-id="c78a6-638">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="c78a6-639">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-639">**Mitigations**</span></span>

<span data-ttu-id="c78a6-640">L'azione più appropriata quando si verifica questo errore consiste nell'individuare la causa radice e nell'interrompere la creazione di numerosi provider di servizi interni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-640">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="c78a6-641">È possibile tuttavia convertire nuovamente l'errore in avviso o ignorarlo tramite una configurazione in `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-641">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="c78a6-642">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-642">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="c78a6-643">Nuovo comportamento per la chiamata di HasOne/HasMany con una singola stringa</span><span class="sxs-lookup"><span data-stu-id="c78a6-643">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="c78a6-644">Problema n. 9171</span><span class="sxs-lookup"><span data-stu-id="c78a6-644">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="c78a6-645">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-645">**Old behavior**</span></span>

<span data-ttu-id="c78a6-646">Prima di EF Core 3.0, il codice che chiama `HasOne` o `HasMany` con una singola stringa era interpretato in modo poco chiaro.</span><span class="sxs-lookup"><span data-stu-id="c78a6-646">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="c78a6-647">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-647">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="c78a6-648">Apparentemente, il codice mette in relazione `Samurai` con un altro tipo di entità tramite la proprietà di navigazione `Entrance`, che può essere privata.</span><span class="sxs-lookup"><span data-stu-id="c78a6-648">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="c78a6-649">In realtà, il codice tenta di creare una relazione con un tipo di entità denominato `Entrance` senza proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-649">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="c78a6-650">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-650">**New behavior**</span></span>

<span data-ttu-id="c78a6-651">A partire da EF Core 3.0, il codice sopra riportato ora esegue quello che avrebbe dovuto fare in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-651">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="c78a6-652">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-652">**Why**</span></span>

<span data-ttu-id="c78a6-653">Il comportamento precedente era molto poco chiaro, soprattutto durante la lettura del codice di configurazione e la ricerca di errori.</span><span class="sxs-lookup"><span data-stu-id="c78a6-653">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="c78a6-654">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-654">**Mitigations**</span></span>

<span data-ttu-id="c78a6-655">Questa modifica causerà problemi solo nelle applicazioni che configurano relazioni in modo esplicito usando stringhe per i nomi dei tipi e senza specificare in modo esplicito la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-655">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="c78a6-656">Non è uno scenario comune.</span><span class="sxs-lookup"><span data-stu-id="c78a6-656">This is not common.</span></span>
<span data-ttu-id="c78a6-657">Il comportamento precedente può essere ottenuto passando esplicitamente `null` per il nome della proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-657">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="c78a6-658">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-658">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="c78a6-659">Il tipo restituito per diversi metodi asincroni è cambiato da Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="c78a6-659">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="c78a6-660">Problema n. 15184</span><span class="sxs-lookup"><span data-stu-id="c78a6-660">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="c78a6-661">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-661">**Old behavior**</span></span>

<span data-ttu-id="c78a6-662">I metodi asincroni seguenti in precedenza restituivano il tipo `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="c78a6-662">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="c78a6-663">`ValueGenerator.NextValueAsync()` (e classi derivate)</span><span class="sxs-lookup"><span data-stu-id="c78a6-663">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="c78a6-664">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-664">**New behavior**</span></span>

<span data-ttu-id="c78a6-665">I metodi indicati in precedenza ora restituiscono il tipo `ValueTask<T>` sullo stesso `T` come in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-665">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="c78a6-666">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-666">**Why**</span></span>

<span data-ttu-id="c78a6-667">Questa modifica riduce il numero delle allocazioni di heap sostenute quando si richiamano questi metodi, con un miglioramento generale delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-667">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="c78a6-668">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-668">**Mitigations**</span></span>

<span data-ttu-id="c78a6-669">Le applicazioni semplicemente in attesa delle API precedenti devono solo essere ricompilate e non sono richieste modifiche del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-669">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="c78a6-670">Per scenari di utilizzo più complessi (ad esempio, il passaggio del tipo `Task` restituito a `Task.WhenAny()`) è richiesto in genere che il tipo `ValueTask<T>` restituito venga convertito in `Task<T>` chiamando `AsTask()` su di esso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-670">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="c78a6-671">Si noti che in questo modo si annulla la riduzione delle allocazioni consentita da questa modifica.</span><span class="sxs-lookup"><span data-stu-id="c78a6-671">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="c78a6-672">L'annotazione Relational:TypeMapping è ora TypeMapping</span><span class="sxs-lookup"><span data-stu-id="c78a6-672">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="c78a6-673">Problema n. 9913</span><span class="sxs-lookup"><span data-stu-id="c78a6-673">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="c78a6-674">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-674">**Old behavior**</span></span>

<span data-ttu-id="c78a6-675">Il nome di annotazione delle annotazioni di mapping del tipo era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="c78a6-675">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="c78a6-676">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-676">**New behavior**</span></span>

<span data-ttu-id="c78a6-677">Il nome di annotazione delle annotazioni di mapping del tipo è ora "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="c78a6-677">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="c78a6-678">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-678">**Why**</span></span>

<span data-ttu-id="c78a6-679">Il mapping dei tipi non viene più usato solo per i provider di database relazionali.</span><span class="sxs-lookup"><span data-stu-id="c78a6-679">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="c78a6-680">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-680">**Mitigations**</span></span>

<span data-ttu-id="c78a6-681">Ciò causa un'interruzione solo nelle applicazioni che accedono al mapping dei tipi direttamente come annotazione. Questa situazione non è comune.</span><span class="sxs-lookup"><span data-stu-id="c78a6-681">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="c78a6-682">L'azione più appropriata per risolvere il problema consiste nell'usare la superficie dell'API per accedere al mapping dei tipi anziché l'annotazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-682">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="c78a6-683">ToTable in un tipo derivato genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="c78a6-683">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="c78a6-684">Problema n. 11811</span><span class="sxs-lookup"><span data-stu-id="c78a6-684">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="c78a6-685">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-685">**Old behavior**</span></span>

<span data-ttu-id="c78a6-686">Nelle versioni precedenti a EF Core 3.0, la chiamata a `ToTable()` in un tipo derivato veniva ignorata poiché soltanto la strategia di mapping dell'ereditarietà era una tabella per gerarchia dove la chiamata non era valida.</span><span class="sxs-lookup"><span data-stu-id="c78a6-686">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="c78a6-687">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-687">**New behavior**</span></span>

<span data-ttu-id="c78a6-688">A partire da EF Core 3.0 e in preparazione all'aggiunta del supporto per la tabella per tipo e per TPC in una versione successiva, la chiamata a `ToTable()` in un tipo derivato genera un'eccezione per evitare una modifica del mapping imprevista in futuro.</span><span class="sxs-lookup"><span data-stu-id="c78a6-688">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="c78a6-689">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-689">**Why**</span></span>

<span data-ttu-id="c78a6-690">Attualmente il mapping di un tipo derivato in una tabella diversa non è un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="c78a6-690">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="c78a6-691">Questa modifica consente di evitare interruzioni future quando l'operazione diventerà un'operazione valida.</span><span class="sxs-lookup"><span data-stu-id="c78a6-691">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="c78a6-692">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-692">**Mitigations**</span></span>

<span data-ttu-id="c78a6-693">Rimuovere qualsiasi tentativo di mapping di tipi derivati in altre tabelle.</span><span class="sxs-lookup"><span data-stu-id="c78a6-693">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="c78a6-694">ForSqlServerHasIndex sostituito con HasIndex</span><span class="sxs-lookup"><span data-stu-id="c78a6-694">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="c78a6-695">Problema n. 12366</span><span class="sxs-lookup"><span data-stu-id="c78a6-695">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="c78a6-696">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-696">**Old behavior**</span></span>

<span data-ttu-id="c78a6-697">Nelle versioni precedenti a EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offriva un metodo per configurare le colonne usate con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-697">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="c78a6-698">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-698">**New behavior**</span></span>

<span data-ttu-id="c78a6-699">A partire da EF Core 3.0, l'uso di `Include` in un indice è supportato a livello relazionale.</span><span class="sxs-lookup"><span data-stu-id="c78a6-699">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="c78a6-700">Usare `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-700">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="c78a6-701">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-701">**Why**</span></span>

<span data-ttu-id="c78a6-702">Questa modifica è stata apportata per consolidare l'API per gli indici con `Include` in un'unica posizione per tutti i provider di database.</span><span class="sxs-lookup"><span data-stu-id="c78a6-702">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="c78a6-703">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-703">**Mitigations**</span></span>

<span data-ttu-id="c78a6-704">Usare la nuova API, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-704">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="c78a6-705">Modifiche dell'API dei metadati</span><span class="sxs-lookup"><span data-stu-id="c78a6-705">Metadata API changes</span></span>

[<span data-ttu-id="c78a6-706">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="c78a6-706">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="c78a6-707">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-707">**New behavior**</span></span>

<span data-ttu-id="c78a6-708">Le proprietà seguenti sono state convertite in metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="c78a6-708">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="c78a6-709">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-709">**Why**</span></span>

<span data-ttu-id="c78a6-710">Questa modifica semplifica l'implementazione delle interfacce menzionate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-710">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="c78a6-711">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-711">**Mitigations**</span></span>

<span data-ttu-id="c78a6-712">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-712">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="c78a6-713">Modifiche dell'API dei metadati specifiche del provider</span><span class="sxs-lookup"><span data-stu-id="c78a6-713">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="c78a6-714">Problema n. 214</span><span class="sxs-lookup"><span data-stu-id="c78a6-714">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="c78a6-715">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-715">**New behavior**</span></span>

<span data-ttu-id="c78a6-716">I metodi di estensione specifici del provider verranno resi flat:</span><span class="sxs-lookup"><span data-stu-id="c78a6-716">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="c78a6-717">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-717">**Why**</span></span>

<span data-ttu-id="c78a6-718">Questa modifica semplifica l'implementazione dei metodi di estensione menzionati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c78a6-718">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="c78a6-719">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-719">**Mitigations**</span></span>

<span data-ttu-id="c78a6-720">Usare i nuovi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-720">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="c78a6-721">EF Core non invia più pragma per l'imposizione della chiave esterna di SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-721">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="c78a6-722">Problema n. 12151</span><span class="sxs-lookup"><span data-stu-id="c78a6-722">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="c78a6-723">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-723">**Old behavior**</span></span>

<span data-ttu-id="c78a6-724">Nelle versioni precedenti a EF Core 3.0, EF Core inviava `PRAGMA foreign_keys = 1` quando veniva aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-724">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="c78a6-725">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-725">**New behavior**</span></span>

<span data-ttu-id="c78a6-726">A partire da EF Core 3.0, EF Core non invia più `PRAGMA foreign_keys = 1` quando viene aperta una connessione a SQLite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-726">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="c78a6-727">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-727">**Why**</span></span>

<span data-ttu-id="c78a6-728">Questa modifica è stata apportata poiché EF Core usa `SQLitePCLRaw.bundle_e_sqlite3` per impostazione predefinita. Ciò significa che l'imposizione della chiave esterna è abilitata per impostazione predefinita e non deve essere abilitata in modo esplicito ogni volta che viene aperta una connessione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-728">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="c78a6-729">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-729">**Mitigations**</span></span>

<span data-ttu-id="c78a6-730">Le chiavi esterne sono abilitate per impostazione predefinita in SQLitePCLRaw.bundle_e_sqlite3, usato per impostazione predefinita per EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-730">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="c78a6-731">Per gli altri casi, è possibile abilitare le chiavi esterne specificando `Foreign Keys=True` nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-731">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="c78a6-732">Microsoft.EntityFrameworkCore.Sqlite dipende ora da SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="c78a6-732">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="c78a6-733">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-733">**Old behavior**</span></span>

<span data-ttu-id="c78a6-734">Nelle versioni precedenti a EF Core 3.0, EF Core usava `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-734">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="c78a6-735">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-735">**New behavior**</span></span>

<span data-ttu-id="c78a6-736">A partire da EF Core 3.0, EF Core usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-736">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="c78a6-737">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-737">**Why**</span></span>

<span data-ttu-id="c78a6-738">Questa modifica è stata apportata per rendere coerente la versione di SQLite usata in iOS con le altre piattaforme.</span><span class="sxs-lookup"><span data-stu-id="c78a6-738">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="c78a6-739">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-739">**Mitigations**</span></span>

<span data-ttu-id="c78a6-740">Per usare la versione di SQLite nativa in iOS, configurare `Microsoft.Data.Sqlite` per l'uso di un'aggregazione `SQLitePCLRaw` diversa.</span><span class="sxs-lookup"><span data-stu-id="c78a6-740">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="c78a6-741">I valori Guid vengono ora archiviati come TEXT in SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-741">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="c78a6-742">Problema n. 15078</span><span class="sxs-lookup"><span data-stu-id="c78a6-742">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="c78a6-743">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-743">**Old behavior**</span></span>

<span data-ttu-id="c78a6-744">I valori Guid in precedenza venivano archiviati come valori BLOB in SQLite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-744">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="c78a6-745">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-745">**New behavior**</span></span>

<span data-ttu-id="c78a6-746">I valori Guid vengono ora archiviati come TEXT.</span><span class="sxs-lookup"><span data-stu-id="c78a6-746">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="c78a6-747">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-747">**Why**</span></span>

<span data-ttu-id="c78a6-748">Il formato binario dei valori Guid non è standardizzato.</span><span class="sxs-lookup"><span data-stu-id="c78a6-748">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="c78a6-749">L'archiviazione dei valori come TEXT rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="c78a6-749">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="c78a6-750">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-750">**Mitigations**</span></span>

<span data-ttu-id="c78a6-751">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-751">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
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

<span data-ttu-id="c78a6-752">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-752">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="c78a6-753">Microsoft.Data.Sqlite rimane in grado di leggere i valori Guid sia da colonne BLOB che TEXT. Tuttavia, poiché è stato modificato il formato predefinito per i parametri e le costanti, probabilmente sarà necessario intervenire per la maggior parte degli scenari che coinvolgono valori Guid.</span><span class="sxs-lookup"><span data-stu-id="c78a6-753">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="c78a6-754">I valori char vengono ora archiviati come testo in SQLite</span><span class="sxs-lookup"><span data-stu-id="c78a6-754">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="c78a6-755">Problema n. 15020</span><span class="sxs-lookup"><span data-stu-id="c78a6-755">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="c78a6-756">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-756">**Old behavior**</span></span>

<span data-ttu-id="c78a6-757">I valori char in precedenza venivano archiviati come valori interi in SQLite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-757">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="c78a6-758">Un valore char di *A* veniva ad esempio archiviato come valore intero 65.</span><span class="sxs-lookup"><span data-stu-id="c78a6-758">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="c78a6-759">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-759">**New behavior**</span></span>

<span data-ttu-id="c78a6-760">I valori char vengono ora archiviati come testo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-760">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="c78a6-761">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-761">**Why**</span></span>

<span data-ttu-id="c78a6-762">L'archiviazione dei valori come testo è un'operazione più naturale e rende il database più compatibile con altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="c78a6-762">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="c78a6-763">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-763">**Mitigations**</span></span>

<span data-ttu-id="c78a6-764">È possibile eseguire la migrazione dei database esistenti al nuovo formato eseguendo SQL nel modo seguente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-764">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="c78a6-765">In EF Core è anche possibile continuare a usare il comportamento precedente configurando un convertitore di valori per queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="c78a6-765">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="c78a6-766">Microsoft.Data.Sqlite rimane comunque in grado di leggere i valori di caratteri presenti sia nelle colonne di valori interi sia in quelle di testo, quindi alcuni scenari potrebbero non richiedere alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-766">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="c78a6-767">Gli ID di migrazione vengono ora generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica</span><span class="sxs-lookup"><span data-stu-id="c78a6-767">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="c78a6-768">Problema n. 12978</span><span class="sxs-lookup"><span data-stu-id="c78a6-768">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="c78a6-769">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-769">**Old behavior**</span></span>

<span data-ttu-id="c78a6-770">Gli ID di migrazione venivano inavvertitamente generati usando il calendario delle impostazioni cultura correnti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-770">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="c78a6-771">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-771">**New behavior**</span></span>

<span data-ttu-id="c78a6-772">Gli ID di migrazione ora vengono sempre generati con il calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica (calendario gregoriano).</span><span class="sxs-lookup"><span data-stu-id="c78a6-772">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="c78a6-773">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-773">**Why**</span></span>

<span data-ttu-id="c78a6-774">L'ordine delle migrazioni è importante quando si esegue l'aggiornamento del database o si risolvono i conflitti di unione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-774">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="c78a6-775">L'uso del calendario delle impostazioni cultura inglese non dipendenti da paese/area geografica evita i problemi che possono verificarsi quando i membri del team hanno calendari di sistema diversi.</span><span class="sxs-lookup"><span data-stu-id="c78a6-775">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="c78a6-776">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-776">**Mitigations**</span></span>

<span data-ttu-id="c78a6-777">Questa modifica interessa gli utenti che usano un calendario non gregoriano in cui l'anno ha un'estensione superiore al calendario gregoriano (come il calendario buddista tailandese).</span><span class="sxs-lookup"><span data-stu-id="c78a6-777">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="c78a6-778">Gli ID di migrazione esistenti dovranno essere aggiornati in modo che le nuove migrazioni vengano collocate dopo le migrazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-778">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="c78a6-779">L'ID di migrazione è disponibile nell'attributo di migrazione presente nei file di progettazione delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-779">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="c78a6-780">È necessario aggiornare anche la tabella della cronologia delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-780">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="c78a6-781">Il metodo UseRowNumberForPaging è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="c78a6-781">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="c78a6-782">Problema n. 16400</span><span class="sxs-lookup"><span data-stu-id="c78a6-782">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="c78a6-783">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-783">**Old behavior**</span></span>

<span data-ttu-id="c78a6-784">Prima di EF Core 3.0 si poteva usare `UseRowNumberForPaging` per generare codice SQL per la suddivisione in pagine compatibile con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="c78a6-784">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="c78a6-785">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-785">**New behavior**</span></span>

<span data-ttu-id="c78a6-786">A partire da EF Core 3.0, EF genererà solo codice SQL per la suddivisione in pagine compatibile solo con versioni successive di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c78a6-786">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="c78a6-787">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-787">**Why**</span></span>

<span data-ttu-id="c78a6-788">Questa modifica è stata apportata perché [SQL Server 2008 non è più un prodotto supportato](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) e l'aggiornamento di questa funzionalità per interagire con le modifiche apportate per le query in EF Core 3.0 è un lavoro significativo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-788">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="c78a6-789">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-789">**Mitigations**</span></span>

<span data-ttu-id="c78a6-790">È consigliabile eseguire l'aggiornamento a una versione più recente di SQL Server o usare un livello di compatibilità superiore, in modo che il codice SQL generato sia supportato.</span><span class="sxs-lookup"><span data-stu-id="c78a6-790">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="c78a6-791">Detto questo, se non è possibile procedere in questo modo, [aggiungere un commento per il problema](https://github.com/aspnet/EntityFrameworkCore/issues/16400) con indicazioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="c78a6-791">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="c78a6-792">Microsoft potrebbe rivedere questa decisione in base ai commenti e suggerimenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-792">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="c78a6-793">Info/metadati dell'estensione rimossi da IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="c78a6-793">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="c78a6-794">Problema n. 16119</span><span class="sxs-lookup"><span data-stu-id="c78a6-794">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="c78a6-795">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-795">**Old behavior**</span></span>

<span data-ttu-id="c78a6-796">`IDbContextOptionsExtension` conteneva metodi per fornire i metadati relativi all'estensione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-796">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="c78a6-797">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-797">**New behavior**</span></span>

<span data-ttu-id="c78a6-798">Questi metodi sono stati spostati in una nuova classe di base astratta `DbContextOptionsExtensionInfo`, restituita da una nuova proprietà `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-798">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="c78a6-799">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-799">**Why**</span></span>

<span data-ttu-id="c78a6-800">Nelle versioni dalla 2.0 alla 3.0 è stato necessario aggiungere o modificare questi metodi più volte.</span><span class="sxs-lookup"><span data-stu-id="c78a6-800">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="c78a6-801">Suddividendoli in una nuova classe di base astratta sarà più facile apportare questo tipo di modifiche senza compromettere il funzionamento delle estensioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-801">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="c78a6-802">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-802">**Mitigations**</span></span>

<span data-ttu-id="c78a6-803">Aggiornare le estensioni per seguire il nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="c78a6-803">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="c78a6-804">Sono disponibili esempi nelle numerose implementazioni di `IDbContextOptionsExtension` per diversi tipi di estensioni nel codice sorgente di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-804">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="c78a6-805">LogQueryPossibleExceptionWithAggregateOperator è stato rinominato</span><span class="sxs-lookup"><span data-stu-id="c78a6-805">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="c78a6-806">Problema n. 10985</span><span class="sxs-lookup"><span data-stu-id="c78a6-806">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="c78a6-807">**Modifica**</span><span class="sxs-lookup"><span data-stu-id="c78a6-807">**Change**</span></span>

<span data-ttu-id="c78a6-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` è stato rinominato in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="c78a6-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="c78a6-809">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-809">**Why**</span></span>

<span data-ttu-id="c78a6-810">Allineamento del nome di questo evento di avviso con tutti gli altri eventi di avviso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-810">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="c78a6-811">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-811">**Mitigations**</span></span>

<span data-ttu-id="c78a6-812">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="c78a6-812">Use the new name.</span></span> <span data-ttu-id="c78a6-813">(Si noti che il numero di ID evento non è stato modificato.)</span><span class="sxs-lookup"><span data-stu-id="c78a6-813">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="c78a6-814">Chiarimenti per l'API per i nomi di vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="c78a6-814">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="c78a6-815">Problema n. 10730</span><span class="sxs-lookup"><span data-stu-id="c78a6-815">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="c78a6-816">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-816">**Old behavior**</span></span>

<span data-ttu-id="c78a6-817">Prima di EF Core 3.0, si faceva riferimento ai nomi di vincolo di chiave esterna semplicemente con "Name".</span><span class="sxs-lookup"><span data-stu-id="c78a6-817">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="c78a6-818">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="c78a6-819">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-819">**New behavior**</span></span>

<span data-ttu-id="c78a6-820">A partire da EF Core 3.0, si fa ora riferimento ai nomi di vincolo di chiave esterna con "ConstraintName".</span><span class="sxs-lookup"><span data-stu-id="c78a6-820">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="c78a6-821">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-821">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="c78a6-822">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-822">**Why**</span></span>

<span data-ttu-id="c78a6-823">Questa modifica introduce coerenza per la denominazione in quest'area e chiarisce anche che si tratta del nome del vincolo di chiave esterna e non del nome della colonna o della proprietà per cui è definita la chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="c78a6-823">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="c78a6-824">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-824">**Mitigations**</span></span>

<span data-ttu-id="c78a6-825">Usare il nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="c78a6-825">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="c78a6-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync sono diventati pubblici</span><span class="sxs-lookup"><span data-stu-id="c78a6-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="c78a6-827">Problema n. 15997</span><span class="sxs-lookup"><span data-stu-id="c78a6-827">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="c78a6-828">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-828">**Old behavior**</span></span>

<span data-ttu-id="c78a6-829">Prima di EF Core 3.0 questi metodi erano protetti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-829">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="c78a6-830">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-830">**New behavior**</span></span>

<span data-ttu-id="c78a6-831">A partire da EF Core 3.0 questi metodi sono pubblici.</span><span class="sxs-lookup"><span data-stu-id="c78a6-831">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="c78a6-832">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-832">**Why**</span></span>

<span data-ttu-id="c78a6-833">Questi metodi vengono usati da EF per determinare se un database viene creato, ma vuoto.</span><span class="sxs-lookup"><span data-stu-id="c78a6-833">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="c78a6-834">Ciò risulta utile all'esterno di EF quando occorre determinare se applicare o meno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c78a6-834">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="c78a6-835">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-835">**Mitigations**</span></span>

<span data-ttu-id="c78a6-836">Modificare l'accessibilità di eventuali override.</span><span class="sxs-lookup"><span data-stu-id="c78a6-836">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="c78a6-837">Microsoft.EntityFrameworkCore.Design è ora un pacchetto DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="c78a6-837">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="c78a6-838">Problema n. 11506</span><span class="sxs-lookup"><span data-stu-id="c78a6-838">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="c78a6-839">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-839">**Old behavior**</span></span>

<span data-ttu-id="c78a6-840">Prima di EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un pacchetto NuGet normale ed era possibile fare riferimento al relativo assembly dai progetti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="c78a6-840">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="c78a6-841">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-841">**New behavior**</span></span>

<span data-ttu-id="c78a6-842">A partire da EF Core 3.0 è un pacchetto DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="c78a6-842">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="c78a6-843">Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che non è più possibile, per impostazione predefinita, fare riferimento al relativo assembly.</span><span class="sxs-lookup"><span data-stu-id="c78a6-843">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="c78a6-844">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-844">**Why**</span></span>

<span data-ttu-id="c78a6-845">Questo pacchetto è destinato solo all'uso in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-845">This package is only intended to be used at design time.</span></span> <span data-ttu-id="c78a6-846">Le applicazioni distribuite non devono farvi riferimento.</span><span class="sxs-lookup"><span data-stu-id="c78a6-846">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="c78a6-847">Questa raccomandazione è rafforzata dall'impostazione del pacchetto come DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="c78a6-847">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="c78a6-848">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-848">**Mitigations**</span></span>

<span data-ttu-id="c78a6-849">Se è necessario fare riferimento a questo pacchetto per eseguire l'override del comportamento della fase di progettazione di EF Core, è possibile aggiornare i metadati dell'elemento PackageReference nel progetto.</span><span class="sxs-lookup"><span data-stu-id="c78a6-849">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="c78a6-850">In presenza di riferimenti transitivi al pacchetto tramite Microsoft.EntityFrameworkCore.Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto per modificare i relativi metadati.</span><span class="sxs-lookup"><span data-stu-id="c78a6-850">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="c78a6-851">Un riferimento esplicito di questo tipo deve essere aggiunto a qualsiasi progetto in cui sono necessari i tipi del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="c78a6-851">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="c78a6-852">Aggiornamento di SQLitePCL.raw alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-852">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="c78a6-853">Problema n. 14824</span><span class="sxs-lookup"><span data-stu-id="c78a6-853">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="c78a6-854">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-854">**Old behavior**</span></span>

<span data-ttu-id="c78a6-855">Microsoft.EntityFrameworkCore.Sqlite dipendeva in precedenza dalla versione 1.1.12 di SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="c78a6-855">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="c78a6-856">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-856">**New behavior**</span></span>

<span data-ttu-id="c78a6-857">Il pacchetto è stato aggiornato in base alla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="c78a6-857">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="c78a6-858">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-858">**Why**</span></span>

<span data-ttu-id="c78a6-859">La versione 2.0.0 di SQLitePCL.raw è destinata a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="c78a6-859">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="c78a6-860">Era in precedenza destinata a .NET Standard 1.1 e ciò richiedeva un notevole impegno di chiusura di pacchetti transitivi per il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="c78a6-860">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="c78a6-861">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-861">**Mitigations**</span></span>

<span data-ttu-id="c78a6-862">SQLitePCL.raw versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-862">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="c78a6-863">Per informazioni dettagliate, vedere le [Note sulla versione](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .</span><span class="sxs-lookup"><span data-stu-id="c78a6-863">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="c78a6-864">NetTopologySuite aggiornato alla versione 2.0.0</span><span class="sxs-lookup"><span data-stu-id="c78a6-864">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="c78a6-865">Problema n. 14825</span><span class="sxs-lookup"><span data-stu-id="c78a6-865">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="c78a6-866">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-866">**Old behavior**</span></span>

<span data-ttu-id="c78a6-867">I pacchetti spaziali dipendevano in precedenza dalla versione 1.15.1 di NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="c78a6-867">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="c78a6-868">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-868">**New behavior**</span></span>

<span data-ttu-id="c78a6-869">Il pacchetto è stato aggiornato in modo da dipendere dalla versione 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="c78a6-869">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="c78a6-870">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-870">**Why**</span></span>

<span data-ttu-id="c78a6-871">La versione 2.0.0 di NetTopologySuite risolve vari problemi di usabilità riscontrati dagli utenti di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c78a6-871">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="c78a6-872">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-872">**Mitigations**</span></span>

<span data-ttu-id="c78a6-873">NetTopologySuite versione 2.0.0 include alcune modifiche che causano un'interruzione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-873">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="c78a6-874">Per informazioni dettagliate, vedere le [Note sulla versione](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .</span><span class="sxs-lookup"><span data-stu-id="c78a6-874">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="c78a6-875">Viene usato Microsoft. Data. SqlClient al posto di System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="c78a6-875">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="c78a6-876">Rilevamento del problema #15636</span><span class="sxs-lookup"><span data-stu-id="c78a6-876">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="c78a6-877">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-877">**Old behavior**</span></span>

<span data-ttu-id="c78a6-878">Microsoft. EntityFrameworkCore. SqlServer in precedenza dipende da System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="c78a6-878">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="c78a6-879">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-879">**New behavior**</span></span>

<span data-ttu-id="c78a6-880">Il pacchetto è stato aggiornato in base a Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="c78a6-880">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="c78a6-881">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-881">**Why**</span></span>

<span data-ttu-id="c78a6-882">Microsoft. Data. SqlClient è il driver di accesso ai dati principale per SQL Server in futuro e System. Data. SqlClient non è più l'obiettivo dello sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-882">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="c78a6-883">Alcune funzionalità importanti, ad esempio Always Encrypted, sono disponibili solo in Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="c78a6-883">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="c78a6-884">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-884">**Mitigations**</span></span>

<span data-ttu-id="c78a6-885">Se il codice prende una dipendenza diretta da System. Data. SqlClient, è necessario modificarlo in modo che faccia riferimento a Microsoft. Data. SqlClient. Poiché i due pacchetti gestiscono un livello molto elevato di compatibilità API, questo dovrebbe essere solo una semplice modifica del pacchetto e dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c78a6-885">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="c78a6-886">Devono essere configurare più relazioni ambigue che fanno riferimento a se stesse</span><span class="sxs-lookup"><span data-stu-id="c78a6-886">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="c78a6-887">Problema n. 13573</span><span class="sxs-lookup"><span data-stu-id="c78a6-887">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="c78a6-888">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-888">**Old behavior**</span></span>

<span data-ttu-id="c78a6-889">Un tipo di entità con più proprietà di navigazione unidirezionale che fanno riferimento a se stesse e più chiavi esterne corrispondenti è stato erroneamente configurato come relazione singola.</span><span class="sxs-lookup"><span data-stu-id="c78a6-889">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="c78a6-890">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-890">For example:</span></span>

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

<span data-ttu-id="c78a6-891">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-891">**New behavior**</span></span>

<span data-ttu-id="c78a6-892">Questo scenario viene ora rilevato nella compilazione del modello e viene generata un'eccezione indicante che il modello è ambiguo.</span><span class="sxs-lookup"><span data-stu-id="c78a6-892">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="c78a6-893">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-893">**Why**</span></span>

<span data-ttu-id="c78a6-894">Il modello risultante era ambiguo e sarà probabilmente errato in questo caso.</span><span class="sxs-lookup"><span data-stu-id="c78a6-894">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="c78a6-895">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-895">**Mitigations**</span></span>

<span data-ttu-id="c78a6-896">Usare la configurazione completa della relazione.</span><span class="sxs-lookup"><span data-stu-id="c78a6-896">Use full configuration of the relationship.</span></span> <span data-ttu-id="c78a6-897">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c78a6-897">For example:</span></span>

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
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="c78a6-898">DbFunction. Schema è una stringa null o vuota che lo configura in modo che sia nello schema predefinito del modello</span><span class="sxs-lookup"><span data-stu-id="c78a6-898">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="c78a6-899">Rilevamento del problema #12757</span><span class="sxs-lookup"><span data-stu-id="c78a6-899">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="c78a6-900">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c78a6-900">**Old behavior**</span></span>

<span data-ttu-id="c78a6-901">Un DbFunction configurato con schema come stringa vuota è stato trattato come funzione predefinita senza uno schema.</span><span class="sxs-lookup"><span data-stu-id="c78a6-901">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="c78a6-902">Il codice seguente, ad esempio, eseguirà il mapping della `DatePart` funzione CLR alla `DATEPART` funzione predefinita in SqlServer.</span><span class="sxs-lookup"><span data-stu-id="c78a6-902">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="c78a6-903">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c78a6-903">**New behavior**</span></span>

<span data-ttu-id="c78a6-904">Tutti i mapping di DbFunction sono considerati mappati alle funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="c78a6-904">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="c78a6-905">Pertanto, il valore stringa vuoto inserisce la funzione all'interno dello schema predefinito per il modello.</span><span class="sxs-lookup"><span data-stu-id="c78a6-905">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="c78a6-906">Che può corrispondere allo schema configurato in modo esplicito tramite l'API Fluent `modelBuilder.HasDefaultSchema()` o `dbo` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="c78a6-906">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="c78a6-907">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c78a6-907">**Why**</span></span>

<span data-ttu-id="c78a6-908">Lo schema precedentemente vuoto era un modo per trattare la funzione è incorporata, ma tale logica è applicabile solo per SqlServer, in cui le funzioni predefinite non appartengono ad alcuno schema.</span><span class="sxs-lookup"><span data-stu-id="c78a6-908">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="c78a6-909">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c78a6-909">**Mitigations**</span></span>

<span data-ttu-id="c78a6-910">Configurare manualmente la conversione di DbFunction per eseguirne il mapping a una funzione predefinita.</span><span class="sxs-lookup"><span data-stu-id="c78a6-910">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
