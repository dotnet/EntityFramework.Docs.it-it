---
title: Pianificare Entity Framework Core 5,0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: a107f901b215e7abaa5905852f81c2d9ab094b98
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238320"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="59ddb-102">Pianificare Entity Framework Core 5,0</span><span class="sxs-lookup"><span data-stu-id="59ddb-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="59ddb-103">Come descritto nel [processo di pianificazione](xref:core/what-is-new/release_planning), l'input è stato raccolto dagli stakeholder in un piano provvisorio per la versione di EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="59ddb-104">Questo piano è ancora in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="59ddb-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="59ddb-105">Niente qui è un impegno.</span><span class="sxs-lookup"><span data-stu-id="59ddb-105">Nothing here is a commitment.</span></span> <span data-ttu-id="59ddb-106">Questo piano è un punto di partenza che si evolverà Man mano che si apprenderanno altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="59ddb-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="59ddb-107">È possibile che venga effettuato il pull di alcuni elementi attualmente non pianificati per 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="59ddb-108">È possibile che vengano apportate alcune operazioni attualmente pianificate per 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="59ddb-109">Informazioni generali</span><span class="sxs-lookup"><span data-stu-id="59ddb-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="59ddb-110">Numero di versione e data di rilascio</span><span class="sxs-lookup"><span data-stu-id="59ddb-110">Version number and release date</span></span>

<span data-ttu-id="59ddb-111">EF Core 5,0 è attualmente pianificata per la versione allo [stesso tempo di .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="59ddb-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="59ddb-112">È stata scelta la versione "5,0" per l'allineamento con .NET 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="59ddb-113">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="59ddb-113">Supported platforms</span></span>

<span data-ttu-id="59ddb-114">EF Core 5,0 è pianificato per essere eseguito su qualsiasi piattaforma .NET 5,0 in base alla [convergenza di queste piattaforme con .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="59ddb-114">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="59ddb-115">Ciò significa che in termini di .NET Standard e il TFM effettivo usato è ancora da definire.</span><span class="sxs-lookup"><span data-stu-id="59ddb-115">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="59ddb-116">EF Core 5,0 non viene eseguito su .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="59ddb-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="59ddb-117">Modifiche di rilievo</span><span class="sxs-lookup"><span data-stu-id="59ddb-117">Breaking changes</span></span>

<span data-ttu-id="59ddb-118">EF Core 5,0 conterrà alcune [modifiche di rilievo](xref:core/what-is-new/ef-core-5.0/breaking-changes), ma saranno molto meno gravi rispetto a quanto accadeva per EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="59ddb-119">Il nostro obiettivo è consentire l'aggiornamento della maggior parte delle applicazioni senza interruzioni.</span><span class="sxs-lookup"><span data-stu-id="59ddb-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="59ddb-120">Si prevede che verranno apportate alcune modifiche di rilievo per i provider di database, in particolare per quanto riguarda il supporto di TPT.</span><span class="sxs-lookup"><span data-stu-id="59ddb-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="59ddb-121">Tuttavia, si prevede che il lavoro per aggiornare un provider per 5,0 sarà inferiore a quello necessario per l'aggiornamento a 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="59ddb-122">Temi</span><span class="sxs-lookup"><span data-stu-id="59ddb-122">Themes</span></span>

<span data-ttu-id="59ddb-123">Sono state estratte alcune aree o temi principali che costituiranno la base per gli investimenti di grandi dimensioni in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="59ddb-124">Mapping molti-a-molti completamente trasparente per convenzione</span><span class="sxs-lookup"><span data-stu-id="59ddb-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="59ddb-125">Sviluppatori leader: @smitpatel , @AndriySvyryd e@lajones</span><span class="sxs-lookup"><span data-stu-id="59ddb-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="59ddb-126">Rilevato da [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="59ddb-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="59ddb-127">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-127">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-128">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-128">Status: In-progress</span></span>

<span data-ttu-id="59ddb-129">Many-to-many è la [funzionalità più richiesta](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~ 506 voti) nel backlog di GitHub.</span><span class="sxs-lookup"><span data-stu-id="59ddb-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="59ddb-130">Il supporto per le relazioni molti-a-molti può essere suddiviso in tre aree principali:</span><span class="sxs-lookup"><span data-stu-id="59ddb-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="59ddb-131">Ignorare le proprietà di navigazione: coperte dal tema successivo.</span><span class="sxs-lookup"><span data-stu-id="59ddb-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="59ddb-132">Tipi di entità del contenitore delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="59ddb-132">Property-bag entity types.</span></span> <span data-ttu-id="59ddb-133">Questi consentono un tipo CLR standard (ad esempio `Dictionary` ) da usare per le istanze di entità in modo che un tipo CLR esplicito non sia necessario per ogni tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="59ddb-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="59ddb-134">Rilevato da [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="59ddb-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="59ddb-135">Sugar per semplificare la configurazione di relazioni molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="59ddb-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="59ddb-136">Oltre al supporto per l'esplorazione, è ora possibile effettuare il pull di queste altre aree di molti-a-molti in EF Core 5,0 per offrire un'esperienza completa.</span><span class="sxs-lookup"><span data-stu-id="59ddb-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="59ddb-137">Proprietà di navigazione molti-a-molti (a. k. a "Ignora navigazione")</span><span class="sxs-lookup"><span data-stu-id="59ddb-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="59ddb-138">Sviluppatori leader: @smitpatel e@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="59ddb-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="59ddb-139">Rilevato da [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="59ddb-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="59ddb-140">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-140">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-141">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-141">Status: In-progress</span></span>

<span data-ttu-id="59ddb-142">Come descritto nel primo tema, il supporto molti-a-molti presenta diversi aspetti.</span><span class="sxs-lookup"><span data-stu-id="59ddb-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="59ddb-143">Questo tema rileva in modo specifico l'uso di esplorazioni Skip.</span><span class="sxs-lookup"><span data-stu-id="59ddb-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="59ddb-144">Il blocco più significativo per coloro che desiderano il supporto molti-a-molti non è in grado di utilizzare le relazioni "naturali", senza fare riferimento alla tabella di join, nella logica di business, ad esempio le query.</span><span class="sxs-lookup"><span data-stu-id="59ddb-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="59ddb-145">Il tipo di entità tabella di join può ancora esistere, ma non dovrebbe essere in grado di ottenere la logica di business.</span><span class="sxs-lookup"><span data-stu-id="59ddb-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="59ddb-146">Mapping di ereditarietà tabella per tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="59ddb-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="59ddb-147">Sviluppatore principale:@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="59ddb-147">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="59ddb-148">Rilevato da [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="59ddb-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="59ddb-149">Dimensioni della maglietta: XL</span><span class="sxs-lookup"><span data-stu-id="59ddb-149">T-shirt size: XL</span></span>

<span data-ttu-id="59ddb-150">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-150">Status: In-progress</span></span>

<span data-ttu-id="59ddb-151">Stiamo eseguendo TPT perché si tratta di una funzionalità estremamente richiesta (~ 289 voti, 3 in generale) e perché richiede alcune modifiche di basso livello che si ritiene siano appropriate per la natura fondamentale del piano generale .NET 5.</span><span class="sxs-lookup"><span data-stu-id="59ddb-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="59ddb-152">Si prevede che questo provocherà modifiche di rilievo per i provider di database, anche se queste devono essere molto meno gravi rispetto alle modifiche necessarie per 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="59ddb-153">Inclusione filtrato</span><span class="sxs-lookup"><span data-stu-id="59ddb-153">Filtered Include</span></span>

<span data-ttu-id="59ddb-154">Sviluppatore principale:@maumar</span><span class="sxs-lookup"><span data-stu-id="59ddb-154">Lead developer: @maumar</span></span>

<span data-ttu-id="59ddb-155">Rilevato da [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="59ddb-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="59ddb-156">Dimensioni T-Shirt: M</span><span class="sxs-lookup"><span data-stu-id="59ddb-156">T-shirt size: M</span></span>

<span data-ttu-id="59ddb-157">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-157">Status: In-progress</span></span>

<span data-ttu-id="59ddb-158">L'inclusione filtrata è una funzionalità molto richiesta (~ 376 voti, 2 nel complesso) che non è una grande quantità di lavoro e che riteniamo che lo sblocco o renderà più semplice molti scenari che attualmente richiedono filtri a livello di modello o query più complesse.</span><span class="sxs-lookup"><span data-stu-id="59ddb-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="59ddb-159">Includi suddivisione</span><span class="sxs-lookup"><span data-stu-id="59ddb-159">Split Include</span></span>

<span data-ttu-id="59ddb-160">Sviluppatore principale:@smitpatel</span><span class="sxs-lookup"><span data-stu-id="59ddb-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="59ddb-161">Rilevato da [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="59ddb-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="59ddb-162">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-162">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-163">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-163">Status: In-progress</span></span>

<span data-ttu-id="59ddb-164">EF Core 3,0 ha modificato il comportamento predefinito per creare una singola query SQL per una query LINQ specificata.</span><span class="sxs-lookup"><span data-stu-id="59ddb-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="59ddb-165">Ciò ha causato regressioni di prestazioni elevate per le query che utilizzano Includi per più raccolte.</span><span class="sxs-lookup"><span data-stu-id="59ddb-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="59ddb-166">In EF Core 5,0, viene mantenuto il nuovo comportamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="59ddb-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="59ddb-167">Tuttavia, EF Core 5,0 consentirà la generazione di più query per la raccolta, inclusi i casi in cui la presenza di una singola query causa un peggioramento delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="59ddb-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="59ddb-168">Razionalizzare ToTable, ToQuery, toview, dati da tabelle e così via</span><span class="sxs-lookup"><span data-stu-id="59ddb-168">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="59ddb-169">Sviluppatori leader: @maumar e@smitpatel</span><span class="sxs-lookup"><span data-stu-id="59ddb-169">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="59ddb-170">Rilevato da [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="59ddb-170">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="59ddb-171">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-171">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-172">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-172">Status: In-progress</span></span>

<span data-ttu-id="59ddb-173">Sono stati apportati miglioramenti alle versioni precedenti per il supporto di SQL non elaborato, tipi autochiave e aree correlate.</span><span class="sxs-lookup"><span data-stu-id="59ddb-173">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="59ddb-174">Tuttavia, vi sono Gap e incoerenze nel modo in cui tutto funziona insieme nel suo complesso.</span><span class="sxs-lookup"><span data-stu-id="59ddb-174">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="59ddb-175">L'obiettivo di 5,0 è correggerli e creare un'esperienza ottimale per la definizione, la migrazione e l'utilizzo di tipi diversi di entità e delle query e degli elementi di database associati.</span><span class="sxs-lookup"><span data-stu-id="59ddb-175">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="59ddb-176">Questo può comportare anche aggiornamenti all'API di query compilata.</span><span class="sxs-lookup"><span data-stu-id="59ddb-176">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="59ddb-177">Si noti che questo elemento può comportare alcune modifiche sostanziali a livello di applicazione, poiché alcune delle funzionalità attualmente disponibili sono troppo permissive, in modo da consentire agli utenti di raggiungere rapidamente i pozzi di errore.</span><span class="sxs-lookup"><span data-stu-id="59ddb-177">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="59ddb-178">È probabile che si stiano bloccando alcune di queste funzionalità insieme a indicazioni sulle operazioni da eseguire.</span><span class="sxs-lookup"><span data-stu-id="59ddb-178">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="59ddb-179">Miglioramenti generali delle query</span><span class="sxs-lookup"><span data-stu-id="59ddb-179">General query enhancements</span></span>

<span data-ttu-id="59ddb-180">Sviluppatori leader: @smitpatel e@maumar</span><span class="sxs-lookup"><span data-stu-id="59ddb-180">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="59ddb-181">Rilevato da [problemi contrassegnati con `area-query` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="59ddb-181">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="59ddb-182">Dimensioni della maglietta: XL</span><span class="sxs-lookup"><span data-stu-id="59ddb-182">T-shirt size: XL</span></span>

<span data-ttu-id="59ddb-183">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-183">Status: In-progress</span></span>

<span data-ttu-id="59ddb-184">Il codice di traduzione query è stato riscritto in maniera estensiva per EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-184">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="59ddb-185">Il codice di query si trova in genere in uno stato molto più solido a causa di questo problema.</span><span class="sxs-lookup"><span data-stu-id="59ddb-185">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="59ddb-186">Per 5,0 non è prevista la creazione di modifiche di query principali, al di fuori di quelle necessarie per supportare TPT e ignorare le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="59ddb-186">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="59ddb-187">Tuttavia, è ancora necessario un lavoro significativo per risolvere alcuni debiti tecnici lasciati dalla revisione 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-187">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="59ddb-188">Si prevede inoltre di correggere molti bug e implementare piccoli miglioramenti per migliorare ulteriormente l'esperienza complessiva della query.</span><span class="sxs-lookup"><span data-stu-id="59ddb-188">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="59ddb-189">Migrazioni ed esperienza di distribuzione</span><span class="sxs-lookup"><span data-stu-id="59ddb-189">Migrations and deployment experience</span></span>

<span data-ttu-id="59ddb-190">Sviluppatori leader:@bricelam</span><span class="sxs-lookup"><span data-stu-id="59ddb-190">Lead developers: @bricelam</span></span>

<span data-ttu-id="59ddb-191">Rilevato da [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="59ddb-191">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="59ddb-192">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-192">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-193">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-193">Status: In-progress</span></span>

<span data-ttu-id="59ddb-194">Attualmente, molti sviluppatori migrano i database al momento dell'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59ddb-194">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="59ddb-195">Questa operazione è semplice, ma non è consigliata perché:</span><span class="sxs-lookup"><span data-stu-id="59ddb-195">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="59ddb-196">Più thread/processi/server possono tentare di eseguire la migrazione simultanea del database</span><span class="sxs-lookup"><span data-stu-id="59ddb-196">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="59ddb-197">Le applicazioni potrebbero provare ad accedere allo stato incoerente durante l'operazione</span><span class="sxs-lookup"><span data-stu-id="59ddb-197">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="59ddb-198">In genere le autorizzazioni per il database per modificare lo schema non devono essere concesse per l'esecuzione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="59ddb-198">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="59ddb-199">È difficile ripristinare uno stato pulito se si verificano problemi</span><span class="sxs-lookup"><span data-stu-id="59ddb-199">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="59ddb-200">Si vuole offrire un'esperienza migliore che consenta di eseguire la migrazione del database in fase di distribuzione in modo semplice.</span><span class="sxs-lookup"><span data-stu-id="59ddb-200">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="59ddb-201">Questa operazione dovrebbe:</span><span class="sxs-lookup"><span data-stu-id="59ddb-201">This should:</span></span>

* <span data-ttu-id="59ddb-202">Lavorare su Linux, Mac e Windows</span><span class="sxs-lookup"><span data-stu-id="59ddb-202">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="59ddb-203">Esperienza ottimale nella riga di comando</span><span class="sxs-lookup"><span data-stu-id="59ddb-203">Be a good experience on the command line</span></span>
* <span data-ttu-id="59ddb-204">Supportare scenari con i contenitori</span><span class="sxs-lookup"><span data-stu-id="59ddb-204">Support scenarios with containers</span></span>
* <span data-ttu-id="59ddb-205">Usare gli strumenti e i flussi di distribuzione reali usati di frequente</span><span class="sxs-lookup"><span data-stu-id="59ddb-205">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="59ddb-206">Integrazione in almeno Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59ddb-206">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="59ddb-207">Il risultato è probabilmente costituito da numerosi miglioramenti di EF Core (ad esempio, migliori migrazioni in SQLite), insieme a linee guida e collaborazioni a lungo termine con altri team per migliorare le esperienze end-to-end che vanno oltre il solo EF.</span><span class="sxs-lookup"><span data-stu-id="59ddb-207">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="59ddb-208">Esperienza delle piattaforme EF Core</span><span class="sxs-lookup"><span data-stu-id="59ddb-208">EF Core platforms experience</span></span>

<span data-ttu-id="59ddb-209">Sviluppatori leader: @roji e@bricelam</span><span class="sxs-lookup"><span data-stu-id="59ddb-209">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="59ddb-210">Rilevato da [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="59ddb-210">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="59ddb-211">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-211">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-212">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="59ddb-212">Status: Not started</span></span>

<span data-ttu-id="59ddb-213">Sono disponibili indicazioni utili per l'uso di EF Core in applicazioni Web tradizionali di tipo MVC.</span><span class="sxs-lookup"><span data-stu-id="59ddb-213">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="59ddb-214">Linee guida per altre piattaforme e modelli di applicazione mancanti o non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="59ddb-214">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="59ddb-215">Per EF Core 5,0, si prevede di analizzare, migliorare e documentare l'esperienza di utilizzo di EF Core con:</span><span class="sxs-lookup"><span data-stu-id="59ddb-215">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="59ddb-216">Blazor</span><span class="sxs-lookup"><span data-stu-id="59ddb-216">Blazor</span></span>
* <span data-ttu-id="59ddb-217">Novell, incluso l'uso della storia AOT/linker</span><span class="sxs-lookup"><span data-stu-id="59ddb-217">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="59ddb-218">WinForms/WPF/WinUI e possibilmente altri U.I.</span><span class="sxs-lookup"><span data-stu-id="59ddb-218">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="59ddb-219">frameworks</span><span class="sxs-lookup"><span data-stu-id="59ddb-219">frameworks</span></span>

<span data-ttu-id="59ddb-220">È probabile che si verifichino molti piccoli miglioramenti in EF Core, insieme a linee guida e collaborazioni a lungo termine con altri team per migliorare le esperienze end-to-end che vanno oltre la semplice EF.</span><span class="sxs-lookup"><span data-stu-id="59ddb-220">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="59ddb-221">Le aree specifiche da esaminare sono:</span><span class="sxs-lookup"><span data-stu-id="59ddb-221">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="59ddb-222">Distribuzione, inclusa l'esperienza di utilizzo degli strumenti EF, ad esempio per le migrazioni</span><span class="sxs-lookup"><span data-stu-id="59ddb-222">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="59ddb-223">Modelli di applicazione, inclusi Novell e blazer, e probabilmente altri</span><span class="sxs-lookup"><span data-stu-id="59ddb-223">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="59ddb-224">Esperienze di SQLite, tra cui l'esperienza spaziale e le ricompilazioni di tabelle</span><span class="sxs-lookup"><span data-stu-id="59ddb-224">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="59ddb-225">Esperienza AOT e collegamento</span><span class="sxs-lookup"><span data-stu-id="59ddb-225">AOT and linking experiences</span></span>
* <span data-ttu-id="59ddb-226">Integrazione di diagnostica, inclusi i contatori delle prestazioni</span><span class="sxs-lookup"><span data-stu-id="59ddb-226">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="59ddb-227">Prestazioni</span><span class="sxs-lookup"><span data-stu-id="59ddb-227">Performance</span></span>

<span data-ttu-id="59ddb-228">Sviluppatore principale:@roji</span><span class="sxs-lookup"><span data-stu-id="59ddb-228">Lead developer: @roji</span></span>

<span data-ttu-id="59ddb-229">Rilevato da [problemi contrassegnati con `area-perf` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="59ddb-229">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="59ddb-230">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-230">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-231">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-231">Status: In-progress</span></span>

<span data-ttu-id="59ddb-232">Per EF Core, si prevede di migliorare la nostra suite di benchmark delle prestazioni e di apportare miglioramenti al runtime.</span><span class="sxs-lookup"><span data-stu-id="59ddb-232">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="59ddb-233">Si prevede inoltre di completare la nuova API batch ADO.NET, che è stata prototipata durante il ciclo di rilascio 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-233">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="59ddb-234">Inoltre, a livello di ADO.NET, vengono pianificati miglioramenti delle prestazioni aggiuntivi per il provider npgsql.</span><span class="sxs-lookup"><span data-stu-id="59ddb-234">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="59ddb-235">Nell'ambito di questo lavoro si prevede anche di aggiungere i contatori delle prestazioni di ADO.NET/EF core e altri dati di diagnostica in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="59ddb-235">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="59ddb-236">Documentazione dell'architettura/collaboratore</span><span class="sxs-lookup"><span data-stu-id="59ddb-236">Architectural/contributor documentation</span></span>

<span data-ttu-id="59ddb-237">Documento principale:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="59ddb-237">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="59ddb-238">Rilevato da [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="59ddb-238">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="59ddb-239">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-239">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-240">Stato: taglia</span><span class="sxs-lookup"><span data-stu-id="59ddb-240">Status: Cut</span></span>

<span data-ttu-id="59ddb-241">L'idea è quella di semplificare la comprensione di ciò che accade negli elementi interni del EF Core.</span><span class="sxs-lookup"><span data-stu-id="59ddb-241">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="59ddb-242">Questa operazione può essere utile per chiunque usi EF Core, ma la motivazione principale consiste nel rendere più semplice per gli utenti esterni:</span><span class="sxs-lookup"><span data-stu-id="59ddb-242">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="59ddb-243">Contribuisci al codice EF Core</span><span class="sxs-lookup"><span data-stu-id="59ddb-243">Contribute to the EF Core code</span></span>
* <span data-ttu-id="59ddb-244">Creare provider di database</span><span class="sxs-lookup"><span data-stu-id="59ddb-244">Create database providers</span></span>
* <span data-ttu-id="59ddb-245">Compila altre estensioni</span><span class="sxs-lookup"><span data-stu-id="59ddb-245">Build other extensions</span></span>

<span data-ttu-id="59ddb-246">Aggiornamento: Sfortunatamente, questo piano era troppo ambizioso.</span><span class="sxs-lookup"><span data-stu-id="59ddb-246">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="59ddb-247">Si ritiene ancora che questo aspetto è importante, ma sfortunatamente non verrà atterrato con EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-247">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="59ddb-248">Documentazione di Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="59ddb-248">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="59ddb-249">Documento principale:@bricelam</span><span class="sxs-lookup"><span data-stu-id="59ddb-249">Lead documenter: @bricelam</span></span>

<span data-ttu-id="59ddb-250">Rilevato da [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="59ddb-250">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="59ddb-251">Dimensioni T-Shirt: M</span><span class="sxs-lookup"><span data-stu-id="59ddb-251">T-shirt size: M</span></span>

<span data-ttu-id="59ddb-252">Stato: completato.</span><span class="sxs-lookup"><span data-stu-id="59ddb-252">Status: Completed.</span></span> <span data-ttu-id="59ddb-253">La nuova documentazione è disponibile nel [sito Microsoft docs](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="59ddb-253">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="59ddb-254">Il team EF possiede anche il provider Microsoft. Data. sqlite ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="59ddb-254">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="59ddb-255">Si prevede di documentare completamente questo provider nell'ambito della versione 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-255">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="59ddb-256">Documentazione generale</span><span class="sxs-lookup"><span data-stu-id="59ddb-256">General documentation</span></span>

<span data-ttu-id="59ddb-257">Documento principale:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="59ddb-257">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="59ddb-258">Rilevato da [problemi nel repository docs nell'attività cardine 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="59ddb-258">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="59ddb-259">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-259">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-260">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-260">Status: In-progress</span></span>

<span data-ttu-id="59ddb-261">È già in corso l'aggiornamento della documentazione per le versioni 3,0 e 3,1.</span><span class="sxs-lookup"><span data-stu-id="59ddb-261">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="59ddb-262">Stiamo lavorando anche:</span><span class="sxs-lookup"><span data-stu-id="59ddb-262">We are also working on:</span></span>

* <span data-ttu-id="59ddb-263">Revisione dei documenti introduttivi per renderli più accessibili o più facili da seguire</span><span class="sxs-lookup"><span data-stu-id="59ddb-263">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="59ddb-264">Riorganizzazione dei documenti per semplificare la ricerca e l'aggiunta di riferimenti incrociati</span><span class="sxs-lookup"><span data-stu-id="59ddb-264">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="59ddb-265">Aggiunta di dettagli e chiarimenti ai documenti esistenti</span><span class="sxs-lookup"><span data-stu-id="59ddb-265">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="59ddb-266">Aggiornamento degli esempi e aggiunta di altri esempi</span><span class="sxs-lookup"><span data-stu-id="59ddb-266">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="59ddb-267">Correzione dei bug</span><span class="sxs-lookup"><span data-stu-id="59ddb-267">Fixing bugs</span></span>

<span data-ttu-id="59ddb-268">Rilevato da [problemi contrassegnati con `type-bug` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="59ddb-268">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="59ddb-269">Sviluppatori: @roji , @maumar , @bricelam , @smitpatel , @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="59ddb-269">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="59ddb-270">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-270">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-271">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-271">Status: In-progress</span></span>

<span data-ttu-id="59ddb-272">Al momento della stesura di questo articolo, sono stati risolti 135 bug nella versione 5,0 (con 62 già corretti), ma si è verificata una sovrapposizione significativa con la sezione relativa ai _miglioramenti generali delle query_ precedente.</span><span class="sxs-lookup"><span data-stu-id="59ddb-272">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="59ddb-273">La velocità in ingresso (problemi che finiscono come lavoro in un'attività cardine) è stata di circa 23 problemi al mese nel corso della versione 3,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-273">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="59ddb-274">Non tutti questi dovranno essere corretti in 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-274">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="59ddb-275">Come stima approssimativa si prevede di correggere altri 150 problemi nell'intervallo di tempo 5,0.</span><span class="sxs-lookup"><span data-stu-id="59ddb-275">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="59ddb-276">Piccoli miglioramenti</span><span class="sxs-lookup"><span data-stu-id="59ddb-276">Small enhancements</span></span>

<span data-ttu-id="59ddb-277">Rilevato da [problemi contrassegnati con `type-enhancement` nell'attività cardine 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="59ddb-277">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="59ddb-278">Sviluppatori: @roji , @maumar , @bricelam , @smitpatel , @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="59ddb-278">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="59ddb-279">Dimensioni della maglietta: L</span><span class="sxs-lookup"><span data-stu-id="59ddb-279">T-shirt size: L</span></span>

<span data-ttu-id="59ddb-280">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="59ddb-280">Status: In-progress</span></span>

<span data-ttu-id="59ddb-281">Oltre alle funzionalità più importanti descritte in precedenza, sono stati riportati anche numerosi miglioramenti più piccoli pianificati per 5,0 per correggere i "tagli di carta".</span><span class="sxs-lookup"><span data-stu-id="59ddb-281">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="59ddb-282">Si noti che molti di questi miglioramenti sono trattati anche dai temi più generali illustrati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="59ddb-282">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="59ddb-283">Sotto la riga</span><span class="sxs-lookup"><span data-stu-id="59ddb-283">Below-the-line</span></span>

<span data-ttu-id="59ddb-284">Rilevato da [problemi con `consider-for-next-release` etichetta](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="59ddb-284">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="59ddb-285">Si tratta di correzioni di bug e miglioramenti che **non** sono attualmente pianificati per la versione 5,0, ma verranno esaminati come obiettivi di estensione a seconda dello stato di avanzamento del lavoro precedente.</span><span class="sxs-lookup"><span data-stu-id="59ddb-285">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="59ddb-286">Inoltre, consideriamo sempre i [problemi più votati](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) durante la pianificazione.</span><span class="sxs-lookup"><span data-stu-id="59ddb-286">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="59ddb-287">Il taglio di uno qualsiasi di questi problemi da un rilascio è sempre penoso, ma è necessario un piano realistico per le risorse disponibili.</span><span class="sxs-lookup"><span data-stu-id="59ddb-287">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="59ddb-288">Suggerimenti</span><span class="sxs-lookup"><span data-stu-id="59ddb-288">Suggestions</span></span>

<span data-ttu-id="59ddb-289">I commenti e i suggerimenti dei clienti sulla pianificazione sono importanti.</span><span class="sxs-lookup"><span data-stu-id="59ddb-289">Your feedback on planning is important.</span></span> <span data-ttu-id="59ddb-290">Il modo migliore per indicare l'importanza di un problema consiste nel votare (Pollice in su) per tale problema in GitHub.</span><span class="sxs-lookup"><span data-stu-id="59ddb-290">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="59ddb-291">Questi dati vengono quindi inseriti nel [processo di pianificazione](xref:core/what-is-new/release_planning) per la versione successiva.</span><span class="sxs-lookup"><span data-stu-id="59ddb-291">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
