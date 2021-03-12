---
title: Pianificare Entity Framework Core 6,0
description: I temi e le funzionalità pianificati per EF Core 6,0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: d6d4f957a57d8ab6a11232b66a5b2a1238df59a3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023484"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="da46e-103">Pianificare Entity Framework Core 6,0</span><span class="sxs-lookup"><span data-stu-id="da46e-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="da46e-104">Come descritto nel [processo di pianificazione](xref:core/what-is-new/release-planning), l'input è stato raccolto dagli stakeholder in un piano per la versione di Entity Framework Core (EF Core) 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="da46e-105">Diversamente dalle versioni precedenti, questo piano non tenta di coprire tutto il lavoro per la versione 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="da46e-106">Indica invece dove e come intendiamo investire in questa versione, ma con la flessibilità necessaria per regolare l'ambito o effettuare il pull di nuove attività quando si raccolgono commenti e suggerimenti e si impara a lavorare al rilascio.</span><span class="sxs-lookup"><span data-stu-id="da46e-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="da46e-107">Questo piano non è un impegno.</span><span class="sxs-lookup"><span data-stu-id="da46e-107">This plan is not a commitment.</span></span> <span data-ttu-id="da46e-108">Si tratta di un punto di partenza che si evolverà Man mano che si apprenderanno altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="da46e-109">È possibile che venga effettuato il pull di alcuni elementi attualmente non pianificati per 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="da46e-110">È possibile che vengano apportate alcune operazioni attualmente pianificate per 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="da46e-111">Informazioni generali</span><span class="sxs-lookup"><span data-stu-id="da46e-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="da46e-112">Numero di versione e data di rilascio</span><span class="sxs-lookup"><span data-stu-id="da46e-112">Version number and release date</span></span>

<span data-ttu-id="da46e-113">EF Core 6,0 è la versione successiva dopo EF Core 5,0 ed è attualmente pianificata per il rilascio nel 2021 novembre allo stesso tempo di .NET 6.</span><span class="sxs-lookup"><span data-stu-id="da46e-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="da46e-114">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="da46e-114">Supported platforms</span></span>

<span data-ttu-id="da46e-115">EF Core 6,0 attualmente è destinato a .NET 5.</span><span class="sxs-lookup"><span data-stu-id="da46e-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="da46e-116">Questa operazione sarà probabilmente aggiornata a .NET 6 come in prossimità della versione.</span><span class="sxs-lookup"><span data-stu-id="da46e-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="da46e-117">EF Core 6,0 non è destinata ad alcuna versione di .NET Standard; Per ulteriori informazioni, vedere [il futuro del .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="da46e-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="da46e-118">EF Core 6,0 non viene eseguito su .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="da46e-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="da46e-119">EF Core 6,0 si allineerà con .NET 6 come [versione di supporto a lungo termine (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="da46e-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="da46e-120">Modifiche che causano un'interruzione</span><span class="sxs-lookup"><span data-stu-id="da46e-120">Breaking changes</span></span>

<span data-ttu-id="da46e-121">EF Core 6,0 conterrà un numero ridotto di [modifiche di rilievo](xref:core/what-is-new/ef-core-6.0/breaking-changes) Man mano che si continua a evolvere sia EF core che la piattaforma .NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="da46e-122">Il nostro obiettivo è consentire l'aggiornamento della maggior parte delle applicazioni senza interruzioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="da46e-123">Temi</span><span class="sxs-lookup"><span data-stu-id="da46e-123">Themes</span></span>

<span data-ttu-id="da46e-124">Le aree seguenti costituiscono la base per gli investimenti di grandi dimensioni in EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="da46e-125">Funzionalità altamente richieste</span><span class="sxs-lookup"><span data-stu-id="da46e-125">Highly requested features</span></span>

<span data-ttu-id="da46e-126">Come sempre, un input principale nel [processo di pianificazione](xref:core/what-is-new/release-planning) deriva dal [Voto ( 👍 ) per le funzionalità di GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="da46e-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="da46e-127">Per EF Core 6,0 si prevede di utilizzare le seguenti funzionalità altamente richieste:</span><span class="sxs-lookup"><span data-stu-id="da46e-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="da46e-128">SQL Server tabelle temporali</span><span class="sxs-lookup"><span data-stu-id="da46e-128">SQL Server temporal tables</span></span>

<span data-ttu-id="da46e-129">Rilevato da [#4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="da46e-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="da46e-130">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-130">Status: Not started</span></span>

<span data-ttu-id="da46e-131">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-131">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-132">Le tabelle temporali supportano le query per i dati archiviati nella tabella in _qualsiasi momento_, anziché solo i dati più recenti archiviati come nel caso delle tabelle normali.</span><span class="sxs-lookup"><span data-stu-id="da46e-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="da46e-133">EF Core 6,0 consentirà la creazione di tabelle temporali tramite migrazioni, oltre a consentire l'accesso ai dati tramite query LINQ.</span><span class="sxs-lookup"><span data-stu-id="da46e-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="da46e-134">Questo lavoro viene inizialmente definito come [descritto nel problema](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span><span class="sxs-lookup"><span data-stu-id="da46e-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="da46e-135">È possibile ottenere supporto aggiuntivo in base ai commenti e suggerimenti durante la versione.</span><span class="sxs-lookup"><span data-stu-id="da46e-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="da46e-136">Colonne JSON</span><span class="sxs-lookup"><span data-stu-id="da46e-136">JSON columns</span></span>

<span data-ttu-id="da46e-137">Rilevato da [#4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="da46e-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="da46e-138">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-138">Status: Not started</span></span>

<span data-ttu-id="da46e-139">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-139">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-140">Questa funzionalità introdurrà un meccanismo comune e i modelli per il supporto JSON che possono essere implementati da qualsiasi provider di database.</span><span class="sxs-lookup"><span data-stu-id="da46e-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="da46e-141">Si collaborerà con la community per allineare le implementazioni esistenti per [npgsql](https://github.com/npgsql/efcore.pg) e [pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), oltre a aggiungere il supporto per SQL Server e SQLite.</span><span class="sxs-lookup"><span data-stu-id="da46e-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="da46e-142">ColumnAttribute. Order</span><span class="sxs-lookup"><span data-stu-id="da46e-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="da46e-143">Rilevato da [#10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="da46e-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="da46e-144">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-144">Status: Not started</span></span>

<span data-ttu-id="da46e-145">Dimensioni della maglietta: Small</span><span class="sxs-lookup"><span data-stu-id="da46e-145">T-shirt size: Small</span></span>

<span data-ttu-id="da46e-146">Questa funzionalità consente l'ordinamento arbitrario delle colonne durante **la creazione di una tabella** con migrazioni o `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="da46e-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="da46e-147">Si noti che per modificare l'ordine delle colonne in una tabella esistente, è necessario ricompilare la tabella e non si tratta di un elemento che si prevede di supportare in qualsiasi EF Core versione.</span><span class="sxs-lookup"><span data-stu-id="da46e-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="da46e-148">Prestazioni</span><span class="sxs-lookup"><span data-stu-id="da46e-148">Performance</span></span>

<span data-ttu-id="da46e-149">Sebbene EF Core sia in genere più veloce di EF6, sono ancora presenti aree in cui è possibile ottenere miglioramenti significativi delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="da46e-150">Si prevede di affrontare diverse di queste aree in EF Core 6,0, migliorando al tempo stesso l'infrastruttura e i test delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="da46e-151">Questo tema implica una grande indagine iterativa, che informa sulla posizione in cui vengono concentrate le risorse.</span><span class="sxs-lookup"><span data-stu-id="da46e-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="da46e-152">Si prevede di iniziare con:</span><span class="sxs-lookup"><span data-stu-id="da46e-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="da46e-153">Infrastruttura di prestazioni e nuovi test</span><span class="sxs-lookup"><span data-stu-id="da46e-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="da46e-154">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-154">Status: Not started</span></span>

<span data-ttu-id="da46e-155">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-155">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-156">Il EF Core codebase contiene già un set di benchmark delle prestazioni che vengono eseguiti ogni giorno.</span><span class="sxs-lookup"><span data-stu-id="da46e-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="da46e-157">Per 6,0, si prevede di migliorare l'infrastruttura per questi test, nonché di aggiungere nuovi test.</span><span class="sxs-lookup"><span data-stu-id="da46e-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="da46e-158">Si profilano anche gli scenari di prestazioni principali e si correggeranno tutti i frutti a bassa impiccagione.</span><span class="sxs-lookup"><span data-stu-id="da46e-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="da46e-159">Modelli compilati</span><span class="sxs-lookup"><span data-stu-id="da46e-159">Compiled models</span></span>

<span data-ttu-id="da46e-160">Rilevato da [#1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="da46e-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="da46e-161">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-161">Status: Not started</span></span>

<span data-ttu-id="da46e-162">Dimensioni T-Shirt: X-Large</span><span class="sxs-lookup"><span data-stu-id="da46e-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="da46e-163">I modelli compilati consentiranno la generazione di una forma compilata del modello EF.</span><span class="sxs-lookup"><span data-stu-id="da46e-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="da46e-164">In questo modo si otterranno prestazioni di avvio migliori, oltre a prestazioni generalmente migliori durante l'accesso al modello.</span><span class="sxs-lookup"><span data-stu-id="da46e-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="da46e-165">Fortune TechEmpower</span><span class="sxs-lookup"><span data-stu-id="da46e-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="da46e-166">Rilevato da [#23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="da46e-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="da46e-167">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-167">Status: Not started</span></span>

<span data-ttu-id="da46e-168">Dimensioni T-Shirt: X-Large</span><span class="sxs-lookup"><span data-stu-id="da46e-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="da46e-169">Sono stati eseguiti i [benchmark TechEmpower](https://www.techempower.com/benchmarks/) standard di settore su .NET rispetto a un database PostgreSQL per diversi anni.</span><span class="sxs-lookup"><span data-stu-id="da46e-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="da46e-170">Il [benchmark Fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) è particolarmente rilevante per gli scenari EF.</span><span class="sxs-lookup"><span data-stu-id="da46e-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="da46e-171">Sono disponibili più varianti di questo benchmark, tra cui:</span><span class="sxs-lookup"><span data-stu-id="da46e-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="da46e-172">Implementazione di che utilizza direttamente ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="da46e-173">Questa è l'implementazione più rapida dei tre elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="da46e-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="da46e-174">Implementazione di che utilizza l'oggetto [elegante](https://www.nuget.org/packages/Dapper/).</span><span class="sxs-lookup"><span data-stu-id="da46e-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="da46e-175">Si tratta di un'operazione più lenta rispetto all'uso diretto di ADO.NET, ma sempre veloce.</span><span class="sxs-lookup"><span data-stu-id="da46e-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="da46e-176">Implementazione di che utilizza EF Core.</span><span class="sxs-lookup"><span data-stu-id="da46e-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="da46e-177">Si tratta attualmente dell'implementazione più lenta dei tre.</span><span class="sxs-lookup"><span data-stu-id="da46e-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="da46e-178">L'obiettivo di EF Core 6,0 è ottenere le prestazioni di EF Core in modo che corrispondano a quelle di elegante nel benchmark TechEmpower fortunes.</span><span class="sxs-lookup"><span data-stu-id="da46e-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="da46e-179">Si tratta di una sfida significativa, ma la nostra soluzione migliore è quella più adatta possibile.</span><span class="sxs-lookup"><span data-stu-id="da46e-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="da46e-180">Linker/AOT</span><span class="sxs-lookup"><span data-stu-id="da46e-180">Linker/AOT</span></span>

<span data-ttu-id="da46e-181">Rilevato da [#10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="da46e-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="da46e-182">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-182">Status: Not started</span></span>

<span data-ttu-id="da46e-183">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-183">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-184">EF Core esegue grandi quantità di generazione di codice in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="da46e-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="da46e-185">Si tratta di un'operazione complessa per i modelli di app che dipendono dall'agitazione dell'albero del linker, ad esempio Novell e blazer, e dalle piattaforme che non consentono la compilazione dinamica, ad esempio iOS.</span><span class="sxs-lookup"><span data-stu-id="da46e-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="da46e-186">Si continuerà a esaminare questo spazio come parte del EF Core 6,0 e si apporteranno miglioramenti mirati come possiamo.</span><span class="sxs-lookup"><span data-stu-id="da46e-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="da46e-187">Tuttavia, non è previsto che il gap venga chiuso completamente nell'intervallo di tempo 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="da46e-188">Migrazioni e distribuzione</span><span class="sxs-lookup"><span data-stu-id="da46e-188">Migrations and deployment</span></span>

<span data-ttu-id="da46e-189">In seguito alle [indagini eseguite per la EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), si prevede di introdurre un supporto migliorato per la gestione delle migrazioni e la distribuzione di database.</span><span class="sxs-lookup"><span data-stu-id="da46e-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="da46e-190">Sono incluse due aree principali:</span><span class="sxs-lookup"><span data-stu-id="da46e-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="da46e-191">Bundle di migrazioni</span><span class="sxs-lookup"><span data-stu-id="da46e-191">Migrations bundles</span></span>

<span data-ttu-id="da46e-192">Rilevato da [#19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="da46e-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="da46e-193">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-193">Status: Not started</span></span>

<span data-ttu-id="da46e-194">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-194">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-195">Un bundle di migrazioni è un eseguibile indipendente che applica le migrazioni a un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="da46e-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="da46e-196">Il comportamento corrisponderà `dotnet ef database update` , ma dovrebbe rendere molto più semplice la distribuzione SSH/Docker/PowerShell, poiché tutto il necessario è contenuto nel singolo eseguibile.</span><span class="sxs-lookup"><span data-stu-id="da46e-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="da46e-197">Gestione delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="da46e-197">Managing migrations</span></span>

<span data-ttu-id="da46e-198">Rilevato da [#22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="da46e-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="da46e-199">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-199">Status: Not started</span></span>

<span data-ttu-id="da46e-200">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-200">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-201">Il numero di migrazioni create per un'applicazione può aumentare fino a diventare un carico.</span><span class="sxs-lookup"><span data-stu-id="da46e-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="da46e-202">Inoltre, queste migrazioni vengono spesso distribuite con l'applicazione anche quando questo non è necessario.</span><span class="sxs-lookup"><span data-stu-id="da46e-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="da46e-203">In EF Core 6,0, si prevede di migliorare questo problema grazie a strumenti e gestione di progetti/assembly migliori.</span><span class="sxs-lookup"><span data-stu-id="da46e-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="da46e-204">Due problemi specifici che si intende affrontare sono la [squash di numerose migrazioni in uno](https://github.com/dotnet/efcore/issues/2174) e [la rigenerazione di uno snapshot del modello pulito](https://github.com/dotnet/efcore/issues/18557).</span><span class="sxs-lookup"><span data-stu-id="da46e-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="da46e-205">Migliorare le funzionalità esistenti e correggere i bug</span><span class="sxs-lookup"><span data-stu-id="da46e-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="da46e-206">Qualsiasi [problema o bug assegnato all'attività cardine 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) è attualmente pianificato per questa versione.</span><span class="sxs-lookup"><span data-stu-id="da46e-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="da46e-207">Sono inclusi numerosi piccoli miglioramenti e correzioni di bug.</span><span class="sxs-lookup"><span data-stu-id="da46e-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="da46e-208">Parità di query EF6</span><span class="sxs-lookup"><span data-stu-id="da46e-208">EF6 query parity</span></span>

<span data-ttu-id="da46e-209">Rilevato da [problemi con etichetta "EF6-parità" e nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="da46e-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="da46e-210">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-210">Status: Not started</span></span>

<span data-ttu-id="da46e-211">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-211">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-212">EF Core 5,0 supporta la maggior parte dei modelli di query supportati da EF6, oltre ai modelli non supportati in EF6.</span><span class="sxs-lookup"><span data-stu-id="da46e-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="da46e-213">Per EF Core 6,0, si prevede di chiudere il gap e di rendere supportato EF Core le query su un vero superset di query EF6 supportate.</span><span class="sxs-lookup"><span data-stu-id="da46e-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="da46e-214">Questa operazione sarà determinata dall'analisi dei Gap, ma include già problemi di GroupBy, ad esempio [translate GroupBy seguito da FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), e query SQL non elaborate per i tipi [primitivi](https://github.com/dotnet/efcore/issues/11624) e non [mappati](https://github.com/dotnet/efcore/issues/10753) .</span><span class="sxs-lookup"><span data-stu-id="da46e-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="da46e-215">Oggetti valore</span><span class="sxs-lookup"><span data-stu-id="da46e-215">Value objects</span></span>

<span data-ttu-id="da46e-216">Rilevato da [#9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="da46e-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="da46e-217">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-217">Status: Not started</span></span>

<span data-ttu-id="da46e-218">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-218">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-219">In precedenza era la visualizzazione del team che possedeva entità, destinate al [supporto aggregato](https://www.martinfowler.com/bliki/DDD_Aggregate.html), sarebbe anche un'approssimazione ragionevole agli [oggetti valore](https://www.martinfowler.com/bliki/ValueObject.html).</span><span class="sxs-lookup"><span data-stu-id="da46e-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="da46e-220">L'esperienza è stata illustrata in questo caso.</span><span class="sxs-lookup"><span data-stu-id="da46e-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="da46e-221">Pertanto, nel EF Core 6,0 si prevede di introdurre un'esperienza migliore in base alle esigenze degli oggetti valore nella progettazione basata su dominio.</span><span class="sxs-lookup"><span data-stu-id="da46e-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="da46e-222">Questo approccio sarà basato sui convertitori di valori anziché sulle entità di proprietà.</span><span class="sxs-lookup"><span data-stu-id="da46e-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="da46e-223">Questo lavoro ha inizialmente un ambito per consentire ai [convertitori di valori con mapping a più colonne](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="da46e-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="da46e-224">È possibile ottenere supporto aggiuntivo in base ai commenti e suggerimenti durante la versione.</span><span class="sxs-lookup"><span data-stu-id="da46e-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="da46e-225">Provider Cosmos database</span><span class="sxs-lookup"><span data-stu-id="da46e-225">Cosmos database provider</span></span>

<span data-ttu-id="da46e-226">Rilevato da [problemi contrassegnati con ' area-Cosmos ' e nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span><span class="sxs-lookup"><span data-stu-id="da46e-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="da46e-227">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-227">Status: Not started</span></span>

<span data-ttu-id="da46e-228">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-228">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-229">Stiamo raccogliendo attivamente commenti e suggerimenti sui miglioramenti da apportare al provider Cosmos nella EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="da46e-230">Questo documento verrà aggiornato in modo più approfondito.</span><span class="sxs-lookup"><span data-stu-id="da46e-230">We will update this document as we learn more.</span></span> <span data-ttu-id="da46e-231">Per il momento, assicurati di votare ( 👍 ) per le funzionalità di Cosmos che ti servono.</span><span class="sxs-lookup"><span data-stu-id="da46e-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="da46e-232">Esporre le convenzioni di compilazione del modello alle applicazioni</span><span class="sxs-lookup"><span data-stu-id="da46e-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="da46e-233">Rilevato da [#214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="da46e-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="da46e-234">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-234">Status: Not started</span></span>

<span data-ttu-id="da46e-235">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-235">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-236">EF Core utilizza un set di convenzioni per la compilazione di un modello da tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="da46e-237">Queste convenzioni sono attualmente controllate dal provider di database.</span><span class="sxs-lookup"><span data-stu-id="da46e-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="da46e-238">In EF Core 6,0, si intende consentire alle applicazioni di eseguire l'hook e modificare tali convenzioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="da46e-239">Bilanciamento del bug zero (ZBB)</span><span class="sxs-lookup"><span data-stu-id="da46e-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="da46e-240">Rilevato da [problemi contrassegnati con `type-bug` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="da46e-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="da46e-241">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-241">Status: In-progress</span></span>

<span data-ttu-id="da46e-242">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-242">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-243">Si prevede di correggere tutti i bug in attesa durante l'intervallo di tempo EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="da46e-244">Alcuni aspetti da tenere presente:</span><span class="sxs-lookup"><span data-stu-id="da46e-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="da46e-245">Questo vale in particolare per i problemi con etichetta [Type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span><span class="sxs-lookup"><span data-stu-id="da46e-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="da46e-246">Vi saranno eccezioni, ad esempio quando il bug richiede una modifica di progettazione o una nuova funzionalità per correggere correttamente.</span><span class="sxs-lookup"><span data-stu-id="da46e-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="da46e-247">Questi problemi verranno contrassegnati con l' `blocked` etichetta.</span><span class="sxs-lookup"><span data-stu-id="da46e-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="da46e-248">Quando necessario, i bug si basano sul rischio, in quanto si avvicina a una versione GA/RTM.</span><span class="sxs-lookup"><span data-stu-id="da46e-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="da46e-249">Funzionalità varie</span><span class="sxs-lookup"><span data-stu-id="da46e-249">Miscellaneous features</span></span>

<span data-ttu-id="da46e-250">Rilevato da [problemi contrassegnati con `type-enhancement` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span><span class="sxs-lookup"><span data-stu-id="da46e-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="da46e-251">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-251">Status: In-progress</span></span>

<span data-ttu-id="da46e-252">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-252">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-253">Le varie funzionalità pianificate per EF 6,0 comprendono, ma non sono limitate a:</span><span class="sxs-lookup"><span data-stu-id="da46e-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="da46e-254">Suddividere una query per le raccolte non di spostamento</span><span class="sxs-lookup"><span data-stu-id="da46e-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="da46e-255">Rilevare semplici tabelle di join in reverse engineering e creare relazioni molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="da46e-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="da46e-256">Completa la ricerca full/text in SQLite e SQL Server</span><span class="sxs-lookup"><span data-stu-id="da46e-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="da46e-257">Indici spaziali SQL Server</span><span class="sxs-lookup"><span data-stu-id="da46e-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="da46e-258">Meccanismo/API per specificare una conversione predefinita per qualsiasi proprietà di un determinato tipo nel modello</span><span class="sxs-lookup"><span data-stu-id="da46e-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="da46e-259">Usare la nuova API per l'invio in batch da ADO.NET</span><span class="sxs-lookup"><span data-stu-id="da46e-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="da46e-260">integrazione .NET</span><span class="sxs-lookup"><span data-stu-id="da46e-260">.NET integration</span></span>

<span data-ttu-id="da46e-261">Il team di EF Core funziona anche con diverse tecnologie correlate ma indipendenti.</span><span class="sxs-lookup"><span data-stu-id="da46e-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="da46e-262">In particolare, si prevede di lavorare su:</span><span class="sxs-lookup"><span data-stu-id="da46e-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="da46e-263">Miglioramenti apportati a System. Data</span><span class="sxs-lookup"><span data-stu-id="da46e-263">Enhancements to System.Data</span></span>

<span data-ttu-id="da46e-264">Rilevato da [problemi nel repository dotnet\runtime con etichetta `area-System.Data` nell'attività cardine 6,0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="da46e-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="da46e-265">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-265">Status: Not started</span></span>

<span data-ttu-id="da46e-266">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-266">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-267">Questo lavoro include:</span><span class="sxs-lookup"><span data-stu-id="da46e-267">This work includes:</span></span>

- <span data-ttu-id="da46e-268">Implementazione della nuova [API batch](https://github.com/dotnet/runtime/issues/28633).</span><span class="sxs-lookup"><span data-stu-id="da46e-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="da46e-269">Continua a collaborare con altri team .NET e la community per comprendere e sviluppare ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="da46e-270">[Standardizzare in DiagnosticSource per la traccia nei componenti System. Data. \*](https://github.com/dotnet/runtime/issues/22336).</span><span class="sxs-lookup"><span data-stu-id="da46e-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="da46e-271">Miglioramenti apportati a Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="da46e-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="da46e-272">Rilevato da [problemi contrassegnati con `type-enhancement` e `area-adonet-sqlite` nell'attività cardine 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span><span class="sxs-lookup"><span data-stu-id="da46e-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="da46e-273">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-273">Status: In-progress</span></span>

<span data-ttu-id="da46e-274">Dimensioni T-Shirt: medie</span><span class="sxs-lookup"><span data-stu-id="da46e-274">T-shirt size: Medium</span></span>

<span data-ttu-id="da46e-275">Sono previsti diversi piccoli miglioramenti per Microsoft. Data. SQLite, incluso il [pool di connessioni](https://github.com/dotnet/efcore/issues/13837) e le [istruzioni preparate](https://github.com/dotnet/efcore/issues/14044) per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="da46e-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="da46e-276">Tipi riferimento nullable</span><span class="sxs-lookup"><span data-stu-id="da46e-276">Nullable reference types</span></span>

<span data-ttu-id="da46e-277">Rilevato da [#14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="da46e-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="da46e-278">Stato: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-278">Status: In-progress</span></span>

<span data-ttu-id="da46e-279">Dimensioni T-Shirt: grandi</span><span class="sxs-lookup"><span data-stu-id="da46e-279">T-shirt size: Large</span></span>

<span data-ttu-id="da46e-280">Il codice EF Core viene annotato per usare i [tipi di riferimento Nullable](/dotnet/csharp/nullable-references).</span><span class="sxs-lookup"><span data-stu-id="da46e-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="da46e-281">Esperimenti e analisi</span><span class="sxs-lookup"><span data-stu-id="da46e-281">Experiments and investigations</span></span>

<span data-ttu-id="da46e-282">Il team EF sta pianificando di investire tempo durante la EF Core 6,0 di tempo di sperimentazione e analisi in due aree.</span><span class="sxs-lookup"><span data-stu-id="da46e-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="da46e-283">Si tratta di un processo di apprendimento e, di conseguenza, non sono previsti risultati concreti per la versione 6,0.</span><span class="sxs-lookup"><span data-stu-id="da46e-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="da46e-284">SqlServer. Core</span><span class="sxs-lookup"><span data-stu-id="da46e-284">SqlServer.Core</span></span>

<span data-ttu-id="da46e-285">Rilevato nel [repository Lab di dati .NET](https://github.com/dotnet/datalab/)</span><span class="sxs-lookup"><span data-stu-id="da46e-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="da46e-286">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-286">Status: Not started</span></span>

<span data-ttu-id="da46e-287">Dimensioni della maglietta: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="da46e-288">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) è un provider di database ADO.NET con funzionalità complete per SQL Server.</span><span class="sxs-lookup"><span data-stu-id="da46e-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="da46e-289">Supporta un'ampia gamma di funzionalità di SQL Server sia in .NET Core che in .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="da46e-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="da46e-290">Tuttavia, è anche una codebase di grandi dimensioni e obsoleta con molte interazioni complesse tra i relativi comportamenti.</span><span class="sxs-lookup"><span data-stu-id="da46e-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="da46e-291">In questo modo è difficile analizzare i potenziali guadagni che possono essere effettuati usando le funzionalità di .NET Core più recenti.</span><span class="sxs-lookup"><span data-stu-id="da46e-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="da46e-292">Viene quindi avviato un esperimento in collaborazione con la community per determinare il potenziale potenziale per un driver di SQL Server altamente performanti per .NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="da46e-293">Gli investimenti in Microsoft. Data. SqlClient non cambiano.</span><span class="sxs-lookup"><span data-stu-id="da46e-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="da46e-294">Continuerà a essere il metodo consigliato per connettersi a SQL Server e SQL Azure, sia con sia senza EF Core.</span><span class="sxs-lookup"><span data-stu-id="da46e-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="da46e-295">Continuerà a supportare le nuove funzionalità di SQL Server appena introdotte.</span><span class="sxs-lookup"><span data-stu-id="da46e-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="da46e-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="da46e-296">GraphQL</span></span>

<span data-ttu-id="da46e-297">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-297">Status: Not started</span></span>

<span data-ttu-id="da46e-298">Dimensioni della maglietta: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="da46e-299">[GraphQL](https://graphql.org/) ha acquisito la trazione negli ultimi anni in un'ampia gamma di piattaforme.</span><span class="sxs-lookup"><span data-stu-id="da46e-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="da46e-300">Si prevede di esaminare lo spazio e di trovare modi per migliorare l'esperienza con .NET.</span><span class="sxs-lookup"><span data-stu-id="da46e-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="da46e-301">Questo comporterà la collaborazione con la community sulla comprensione e il supporto dell'ecosistema esistente.</span><span class="sxs-lookup"><span data-stu-id="da46e-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="da46e-302">Potrebbe inoltre comportare un investimento specifico di Microsoft, sotto forma di contributi a lavoro esistente o di sviluppo di parti gratuite nello stack Microsoft.</span><span class="sxs-lookup"><span data-stu-id="da46e-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="da46e-303">Dataverse (in precedenza Common Data Services)</span><span class="sxs-lookup"><span data-stu-id="da46e-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="da46e-304">Stato: non avviato</span><span class="sxs-lookup"><span data-stu-id="da46e-304">Status: Not started</span></span>

<span data-ttu-id="da46e-305">Dimensioni della maglietta: in corso</span><span class="sxs-lookup"><span data-stu-id="da46e-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="da46e-306">[Dataverse](/powerapps/maker/data-platform/data-platform-intro) è un archivio dati basato su colonne progettato per lo sviluppo rapido di applicazioni aziendali.</span><span class="sxs-lookup"><span data-stu-id="da46e-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="da46e-307">Gestisce automaticamente tipi di dati complessi come oggetti binari (BLOB) e include entità e relazioni predefinite, come organizzazioni e contatti.</span><span class="sxs-lookup"><span data-stu-id="da46e-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="da46e-308">È disponibile un SDK, ma gli sviluppatori possono trarre vantaggio dalla presenza di un provider EF Core per l'uso di query LINQ avanzate, sfruttare i vantaggi dell'unità di lavoro e disporre di un'API di accesso ai dati coerente.</span><span class="sxs-lookup"><span data-stu-id="da46e-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="da46e-309">Il team considererà diversi modi per migliorare l'esperienza di sviluppo .NET che si connette a dataverse.</span><span class="sxs-lookup"><span data-stu-id="da46e-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="da46e-310">Sotto-linea-taglia</span><span class="sxs-lookup"><span data-stu-id="da46e-310">Below-the-cut-line</span></span>

<span data-ttu-id="da46e-311">Rilevato da [problemi con `consider-for-current-release` etichetta](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span><span class="sxs-lookup"><span data-stu-id="da46e-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="da46e-312">Si tratta di correzioni di bug e miglioramenti che **non** sono attualmente pianificati per la versione 6,0, ma verranno esaminati in base ai commenti e suggerimenti in tutta la versione insieme ai progressi apportati al lavoro precedente.</span><span class="sxs-lookup"><span data-stu-id="da46e-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="da46e-313">Questi problemi possono essere inseriti EF Core 6,0 e diventano automaticamente candidati per la versione successiva.</span><span class="sxs-lookup"><span data-stu-id="da46e-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="da46e-314">Inoltre, consideriamo sempre i [problemi più votati](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) durante la pianificazione.</span><span class="sxs-lookup"><span data-stu-id="da46e-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="da46e-315">Il taglio di uno qualsiasi di questi problemi da un rilascio è sempre penoso, ma è necessario un piano realistico per le risorse disponibili.</span><span class="sxs-lookup"><span data-stu-id="da46e-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="da46e-316">Assicurarsi di aver votato ( 👍 ) per le funzionalità necessarie.</span><span class="sxs-lookup"><span data-stu-id="da46e-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="da46e-317">Suggerimenti</span><span class="sxs-lookup"><span data-stu-id="da46e-317">Suggestions</span></span>

<span data-ttu-id="da46e-318">I commenti e i suggerimenti dei clienti sulla pianificazione sono importanti.</span><span class="sxs-lookup"><span data-stu-id="da46e-318">Your feedback on planning is important.</span></span> <span data-ttu-id="da46e-319">Il modo migliore per indicare l'importanza di un problema consiste nel votare ( 👍 ) per il problema in GitHub.</span><span class="sxs-lookup"><span data-stu-id="da46e-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="da46e-320">Questi dati vengono quindi inseriti nel [processo di pianificazione](xref:core/what-is-new/release-planning) per la versione successiva.</span><span class="sxs-lookup"><span data-stu-id="da46e-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
