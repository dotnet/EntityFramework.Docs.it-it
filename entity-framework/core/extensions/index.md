---
title: Strumenti ed estensioni - EF Core
description: Strumenti ed estensioni esterni per Entity Framework Core
author: ErikEJ
ms.date: 02/21/2021
uid: core/extensions/index
ms.openlocfilehash: 67d078250d8bfdb845cf98949ee1412a8fe842bd
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024381"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="1df01-103">Strumenti ed estensioni di EF Core</span><span class="sxs-lookup"><span data-stu-id="1df01-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="1df01-104">Questi strumenti ed estensioni offrono funzionalità aggiuntive per Entity Framework Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1df01-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1df01-105">Le estensioni sono composte da diversi tipi di origine e non vengono mantenute nell'ambito del progetto di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="1df01-106">Quando si prende in considerazione un'estensione di terze parti, valutarne con cura gli aspetti relativi a qualità, licenze, compatibilità, supporto e così via, per essere certi che soddisfi i propri requisiti.</span><span class="sxs-lookup"><span data-stu-id="1df01-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="1df01-107">In particolare, un'estensione compilata per una versione precedente di EF Core potrebbe necessitare di un aggiornamento prima che funzioni con le versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="1df01-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="1df01-108">Strumenti</span><span class="sxs-lookup"><span data-stu-id="1df01-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="1df01-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="1df01-109">LLBLGen Pro</span></span>

<span data-ttu-id="1df01-110">LLBLGen Pro è una soluzione per la modellazione delle entità con supporto per Entity Framework ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="1df01-111">Consente di definire facilmente il modello di entità e di eseguirne il mapping al database, usando l'approccio Database-First o Model-First, in modo da iniziare subito a scrivere le query.</span><span class="sxs-lookup"><span data-stu-id="1df01-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="1df01-112">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-113">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1df01-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="1df01-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="1df01-114">Devart Entity Developer</span></span>

<span data-ttu-id="1df01-115">Entity Developer è una potente finestra di progettazione di O/RM per ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access e LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="1df01-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="1df01-116">Supporta la progettazione visiva di modelli EF Core, usando l'approccio con precedenza del modello o precedenza del database e la generazione di codice C# o Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="1df01-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="1df01-117">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-118">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1df01-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="1df01-119">ORM nHydrate per Entity Framework</span><span class="sxs-lookup"><span data-stu-id="1df01-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="1df01-120">Oggetto O/RM che crea classi estendibili e fortemente tipizzate per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1df01-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="1df01-121">Il codice generato è Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="1df01-122">Non c'è alcuna differenza.</span><span class="sxs-lookup"><span data-stu-id="1df01-122">There is no difference.</span></span> <span data-ttu-id="1df01-123">Non si tratta di una sostituzione per EF o un O/RM personalizzato.</span><span class="sxs-lookup"><span data-stu-id="1df01-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="1df01-124">Si tratta di un livello di modellazione visivo che consente a un team di gestire schemi di database complessi.</span><span class="sxs-lookup"><span data-stu-id="1df01-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="1df01-125">Funziona bene con software SCM come Git, consentendo l'accesso multiutente al modello con conflitti minimi.</span><span class="sxs-lookup"><span data-stu-id="1df01-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="1df01-126">Il programma di installazione tiene traccia delle modifiche del modello e crea gli script di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="1df01-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="1df01-127">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-127">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-128">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-128">Github repository</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="1df01-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="1df01-129">EF Core Power Tools</span></span>

<span data-ttu-id="1df01-130">EF Core Power Tools è un'estensione di Visual Studio che espone varie attività di progettazione di EF Core in un'interfaccia utente intuitiva.</span><span class="sxs-lookup"><span data-stu-id="1df01-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="1df01-131">Include la decompilazione di classi DbContext e classi di entità da database esistenti e [pacchetti di applicazione livello dati SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), la gestione delle migrazioni del database e le visualizzazioni del modello.</span><span class="sxs-lookup"><span data-stu-id="1df01-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="1df01-132">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-133">Wiki di GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="1df01-134">Entity Framework Visual Editor</span><span class="sxs-lookup"><span data-stu-id="1df01-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="1df01-135">Entity Framework editor visivo è un'estensione di Visual Studio che aggiunge una finestra di progettazione O/RM per la progettazione visiva di EF 6 e le classi EF Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="1df01-136">Il codice viene generato usando i modelli T4, pertanto può essere personalizzato per soddisfare qualsiasi esigenza.</span><span class="sxs-lookup"><span data-stu-id="1df01-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="1df01-137">Supporta l'ereditarietà, le associazioni unidirezionali e bidirezionali, le enumerazioni e la possibilità di usare una codifica a colori per le classi e di aggiungere blocchi di testo, per spiegare parti potenzialmente molto complesse del progetto.</span><span class="sxs-lookup"><span data-stu-id="1df01-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="1df01-138">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-138">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="1df01-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="1df01-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="1df01-140">CatFactory</span></span>

<span data-ttu-id="1df01-141">CatFactory è un motore di scaffolding per .NET Core in grado di automatizzare la generazione di classi, entità, configurazioni di mapping e classi repository DbContext da un database SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1df01-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="1df01-142">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1df01-142">For EF Core: 2.</span></span>

[<span data-ttu-id="1df01-143">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="1df01-144">Generatore Entity Framework Core di LoreSoft</span><span class="sxs-lookup"><span data-stu-id="1df01-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="1df01-145">Il Generatore Entity Framework Core (efg) è uno strumento della riga di comando di .NET Core che genera modelli EF Core da un database esistente con modalità simili a `dotnet ef dbcontext scaffold`, ma supporta anche la [rigenerazione](https://efg.loresoft.com/en/latest/regeneration/) del codice sicuro tramite la sostituzione dell'area o tramite l'analisi dei file di mapping.</span><span class="sxs-lookup"><span data-stu-id="1df01-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="1df01-146">Lo strumento supporta la generazione di modelli di visualizzazione, la convalida e il codice mapper degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="1df01-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="1df01-147">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1df01-147">For EF Core: 2.</span></span>

<span data-ttu-id="1df01-148">[Esercitazione](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentazione](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="1df01-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="1df01-149">Estensioni</span><span class="sxs-lookup"><span data-stu-id="1df01-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="1df01-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="1df01-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="1df01-151">Libreria di plug-in che consente di registrare automaticamente le modifiche ai dati eseguite da EF Core in una tabella di cronologia.</span><span class="sxs-lookup"><span data-stu-id="1df01-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="1df01-152">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-153">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="1df01-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="1df01-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="1df01-155">La memorizzazione nella cache di secondo livello è una cache della query.</span><span class="sxs-lookup"><span data-stu-id="1df01-155">Second level caching is a query cache.</span></span> <span data-ttu-id="1df01-156">I risultati dei comandi EF verranno memorizzati nella cache, in modo che gli stessi comandi EF recupereranno i dati dalla cache anziché eseguirli di nuovo sul database.</span><span class="sxs-lookup"><span data-stu-id="1df01-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="1df01-157">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-158">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="1df01-159">Geco</span><span class="sxs-lookup"><span data-stu-id="1df01-159">Geco</span></span>

<span data-ttu-id="1df01-160">Geco (Generator Console) è un generatore di codice semplice basato su un progetto di console, che viene eseguito in .NET Core e usa stringhe interpolate C# per la generazione di codice.</span><span class="sxs-lookup"><span data-stu-id="1df01-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="1df01-161">Geco include un generatore di modelli inversi per EF Core con supporto di pluralizzazione, singolarizzazione e modelli modificabili.</span><span class="sxs-lookup"><span data-stu-id="1df01-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="1df01-162">Include anche un generatore di script per dati di inizializzazione e uno strumento di pulizia database.</span><span class="sxs-lookup"><span data-stu-id="1df01-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="1df01-163">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1df01-163">For EF Core: 2.</span></span>

[<span data-ttu-id="1df01-164">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="1df01-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="1df01-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="1df01-166">Consente la personalizzazione di classi decompilate da un database esistente usando la toolchain di Entity Framework Core con modelli Handlebars.</span><span class="sxs-lookup"><span data-stu-id="1df01-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="1df01-167">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-168">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="1df01-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1df01-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="1df01-170">NeinLinq estende i provider LINQ, ad esempio Entity Framework, e consente il riuso delle funzioni, la riscrittura di query e la creazione di query dinamiche usando predicati e selettori traducibili.</span><span class="sxs-lookup"><span data-stu-id="1df01-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="1df01-171">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-172">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="1df01-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="1df01-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="1df01-174">Plug-in per Microsoft.EntityFrameworkCore che supporta repository, modelli di unità di lavoro e più database con la transazione distribuita supportata.</span><span class="sxs-lookup"><span data-stu-id="1df01-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="1df01-175">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-176">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="1df01-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="1df01-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="1df01-178">Estensioni EF Core per operazioni in blocco (inserimento, aggiornamento, eliminazione).</span><span class="sxs-lookup"><span data-stu-id="1df01-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="1df01-179">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-180">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="1df01-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="1df01-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="1df01-182">Aggiunge la pluralizzazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="1df01-182">Adds design-time pluralization.</span></span> <span data-ttu-id="1df01-183">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-184">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="1df01-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="1df01-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="1df01-186">Ripresa dell'attributo [Index] (con estensione per la compilazione del modello).</span><span class="sxs-lookup"><span data-stu-id="1df01-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="1df01-187">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-188">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="verifyentityframework"></a><span data-ttu-id="1df01-189">Verificare. EntityFramework</span><span class="sxs-lookup"><span data-stu-id="1df01-189">Verify.EntityFramework</span></span>

<span data-ttu-id="1df01-190">Estende [Verifica](https://github.com/VerifyTests/Verify) per consentire il test di snapshot con EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="1df01-190">Extends [Verify](https://github.com/VerifyTests/Verify) to allow snapshot testing with EntityFramework.</span></span> <span data-ttu-id="1df01-191">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-191">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-192">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-192">GitHub repository</span></span>](https://github.com/VerifyTests/Verify.EntityFramework)

### <a name="localdb"></a><span data-ttu-id="1df01-193">LocalDb</span><span class="sxs-lookup"><span data-stu-id="1df01-193">LocalDb</span></span>

<span data-ttu-id="1df01-194">Fornisce un wrapper per [SQL Server Express database locale](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) per semplificare l'esecuzione di test su Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1df01-194">Provides a wrapper around [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) to simplify running tests against Entity Framework.</span></span> <span data-ttu-id="1df01-195">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-195">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-196">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-196">GitHub repository</span></span>](https://github.com/SimonCropp/LocalDb)

### <a name="effluentvalidation"></a><span data-ttu-id="1df01-197">EfFluentValidation</span><span class="sxs-lookup"><span data-stu-id="1df01-197">EfFluentValidation</span></span>

<span data-ttu-id="1df01-198">Aggiunge il supporto [alla fluentvalidation](https://fluentvalidation.net/) per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1df01-198">Adds [FluentValidation](https://fluentvalidation.net/) support to Entity Framework.</span></span> <span data-ttu-id="1df01-199">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-199">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-200">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-200">GitHub repository</span></span>](https://github.com/SimonCropp/EfFluentValidation)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="1df01-201">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="1df01-201">EFCore.TemporalSupport</span></span>

<span data-ttu-id="1df01-202">Implementazione di supporto temporale.</span><span class="sxs-lookup"><span data-stu-id="1df01-202">An implementation of temporal support.</span></span> <span data-ttu-id="1df01-203">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1df01-203">For EF Core: 2.</span></span>

[<span data-ttu-id="1df01-204">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-204">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="1df01-205">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="1df01-205">EfCoreTemporalTable</span></span>

<span data-ttu-id="1df01-206">Consente di eseguire facilmente query temporali nel database preferito usando metodi di estensione introdotti: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="1df01-206">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="1df01-207">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-207">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-208">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-208">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="1df01-209">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="1df01-209">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="1df01-210">Libreria di estensioni per Entity Framework Core che consente agli sviluppatori che usano SQL Server di usare facilmente le tabelle temporali.</span><span class="sxs-lookup"><span data-stu-id="1df01-210">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="1df01-211">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-211">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-212">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-212">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="1df01-213">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="1df01-213">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="1df01-214">Cache della query di secondo livello ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="1df01-214">A high-performance second-level query cache.</span></span> <span data-ttu-id="1df01-215">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1df01-215">For EF Core: 2.</span></span>

[<span data-ttu-id="1df01-216">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-216">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="1df01-217">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="1df01-217">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="1df01-218">Il provider Entity Framework Core NCache è un provider di cache di secondo livello distribuito per la memorizzazione nella cache dei risultati delle query.</span><span class="sxs-lookup"><span data-stu-id="1df01-218">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="1df01-219">L'architettura distribuita di NCache ne migliora la scalabilità e offre disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="1df01-219">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="1df01-220">Per EF Core 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-220">For EF Core 2, 3.</span></span>

[<span data-ttu-id="1df01-221">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1df01-221">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="1df01-222">EntityFrameworkCore. triggered</span><span class="sxs-lookup"><span data-stu-id="1df01-222">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="1df01-223">Trigger per EF Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-223">Triggers for EF Core.</span></span> <span data-ttu-id="1df01-224">Rispondere alle modifiche apportate in DbContext prima e dopo che ne è stato eseguito il commit nel database.</span><span class="sxs-lookup"><span data-stu-id="1df01-224">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="1df01-225">I trigger sono completamente asincroni e supportano l'inserimento delle dipendenze, l'ereditarietà, la propagazione e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="1df01-225">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="1df01-226">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-226">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-227">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-227">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="1df01-228">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="1df01-228">Entity Framework Plus</span></span>

<span data-ttu-id="1df01-229">Estende DbContext con funzionalità quali: filtro di inclusione, controllo, memorizzazione nella cache, query Future, eliminazione in batch, aggiornamento in batch e molte altre.</span><span class="sxs-lookup"><span data-stu-id="1df01-229">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="1df01-230">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-230">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="1df01-231">[Sito Web](https://entityframework-plus.net/)
[Repository GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="1df01-231">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="1df01-232">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="1df01-232">Entity Framework Extensions</span></span>

<span data-ttu-id="1df01-233">Estende DbContext con operazioni in blocco ad alte prestazioni: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge e altre.</span><span class="sxs-lookup"><span data-stu-id="1df01-233">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="1df01-234">Per EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-234">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="1df01-235">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1df01-235">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="1df01-236">Expressionify</span><span class="sxs-lookup"><span data-stu-id="1df01-236">Expressionify</span></span>

<span data-ttu-id="1df01-237">Aggiunta del supporto per la chiamata di metodi di estensione nelle espressioni lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="1df01-237">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="1df01-238">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-238">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-239">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-239">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="1df01-240">ELinq</span><span class="sxs-lookup"><span data-stu-id="1df01-240">ELinq</span></span>

<span data-ttu-id="1df01-241">Tecnologia LINQ (Language Integrated Query) per database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1df01-241">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="1df01-242">Consente di usare C# per scrivere query fortemente tipizzate.</span><span class="sxs-lookup"><span data-stu-id="1df01-242">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="1df01-243">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-243">For EF Core: 3.</span></span>

- <span data-ttu-id="1df01-244">Supporto C# completo per la creazione di query: più istruzioni all'interno di lambda, variabili, funzioni e così via.</span><span class="sxs-lookup"><span data-stu-id="1df01-244">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="1df01-245">Nessun gap semantico con SQL.</span><span class="sxs-lookup"><span data-stu-id="1df01-245">No semantic gap with SQL.</span></span> <span data-ttu-id="1df01-246">ELinq dichiara istruzioni SQL (ad esempio `SELECT` , `FROM` , `WHERE` ) come metodi C# di prima classe, combinando sintassi familiare con IntelliSense, indipendenza dai tipi e refactoring.</span><span class="sxs-lookup"><span data-stu-id="1df01-246">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="1df01-247">Di conseguenza, SQL diventa semplicemente "un'altra" libreria di classi che espone l'API in locale, letteralmente *"SQL integrato nel linguaggio"* .</span><span class="sxs-lookup"><span data-stu-id="1df01-247">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="1df01-248">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1df01-248">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="1df01-249">Ramses</span><span class="sxs-lookup"><span data-stu-id="1df01-249">Ramses</span></span>

<span data-ttu-id="1df01-250">Hook del ciclo di vita (per SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="1df01-250">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="1df01-251">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-251">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1df01-252">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-252">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="1df01-253">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="1df01-253">EFCore.NamingConventions</span></span>

<span data-ttu-id="1df01-254">Per tutti i nomi di tabella e colonna verrà automaticamente usata la convenzione di denominazione snake_case, tutte maiuscole o tutte minuscole.</span><span class="sxs-lookup"><span data-stu-id="1df01-254">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="1df01-255">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-255">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-256">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-256">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="1df01-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="1df01-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="1df01-258">Aggiunge il supporto nativo a EntityFrameworkCore per SQL Server per i tipi NodaTime.</span><span class="sxs-lookup"><span data-stu-id="1df01-258">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="1df01-259">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-259">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-260">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-260">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="1df01-261">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="1df01-261">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="1df01-262">Estensioni LINQ per Entity Framework Core 3.1 per supportare le query di tabelle temporali di Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1df01-262">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="1df01-263">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-263">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-264">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-264">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="1df01-265">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="1df01-265">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="1df01-266">Aggiunge il supporto di hierarchyid al provider SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="1df01-266">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="1df01-267">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-267">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-268">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-268">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="1df01-269">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1df01-269">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="1df01-270">Convertitore alternativo di query LINQ in espressioni SQL.</span><span class="sxs-lookup"><span data-stu-id="1df01-270">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="1df01-271">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-271">For EF Core: 3, 5.</span></span>

<span data-ttu-id="1df01-272">Include il supporto per le funzionalità avanzate di SQL, ad esempio espressioni di tabella comuni, copia bulk, hint di tabella, funzioni finestra, tabelle temporanee e operazioni di creazione/aggiornamento/eliminazione sul lato database.</span><span class="sxs-lookup"><span data-stu-id="1df01-272">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="1df01-273">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-273">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="1df01-274">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="1df01-274">EFCore.SoftDelete</span></span>

<span data-ttu-id="1df01-275">Implementazione per l'eliminazione temporanea di entità.</span><span class="sxs-lookup"><span data-stu-id="1df01-275">An implementation for soft deleting entities.</span></span> <span data-ttu-id="1df01-276">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-276">For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="1df01-277">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="1df01-278">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="1df01-278">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="1df01-279">Estende EF Core per risolvere le stringhe di connessione da App.config. Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1df01-279">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="1df01-280">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-280">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="1df01-281">Mapper scollegato</span><span class="sxs-lookup"><span data-stu-id="1df01-281">Detached Mapper</span></span>

<span data-ttu-id="1df01-282">Un DTO-Entity Mapper con gestione composizione/aggregazione (simile a GraphDiff).</span><span class="sxs-lookup"><span data-stu-id="1df01-282">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="1df01-283">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-283">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-284">NuGet</span><span class="sxs-lookup"><span data-stu-id="1df01-284">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)

### <a name="entityframeworkcoresqlitenodatime"></a><span data-ttu-id="1df01-285">EntityFrameworkCore. sqlite. NodaTime</span><span class="sxs-lookup"><span data-stu-id="1df01-285">EntityFrameworkCore.Sqlite.NodaTime</span></span>

<span data-ttu-id="1df01-286">Aggiunge il supporto per i tipi [NodaTime](https://nodatime.org) quando si usa [SQLite](https://sqlite.org).</span><span class="sxs-lookup"><span data-stu-id="1df01-286">Adds support for [NodaTime](https://nodatime.org) types when using [SQLite](https://sqlite.org).</span></span> <span data-ttu-id="1df01-287">Per EF Core: 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-287">For EF Core: 5.</span></span>

[<span data-ttu-id="1df01-288">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-288">GitHub repository</span></span>](https://github.com/khellang/EFCore.Sqlite.NodaTime)

### <a name="erikejentityframeworkcoresqlserverdacpac"></a><span data-ttu-id="1df01-289">ErikEJ. EntityFrameworkCore. SqlServer. dacpac</span><span class="sxs-lookup"><span data-stu-id="1df01-289">ErikEJ.EntityFrameworkCore.SqlServer.Dacpac</span></span>

<span data-ttu-id="1df01-290">Abilita reverse engineering un modello di EF Core da un pacchetto di applicazione livello dati SQL Server (dacpac).</span><span class="sxs-lookup"><span data-stu-id="1df01-290">Enables reverse engineering an EF Core model from a SQL Server data-tier application package (.dacpac).</span></span> <span data-ttu-id="1df01-291">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-291">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-292">Wiki di GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-292">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/ErikEJ.EntityFrameworkCore.SqlServer.Dacpac)

### <a name="erikejentityframeworkcoredgmlbuilder"></a><span data-ttu-id="1df01-293">ErikEJ. EntityFrameworkCore. DgmlBuilder</span><span class="sxs-lookup"><span data-stu-id="1df01-293">ErikEJ.EntityFrameworkCore.DgmlBuilder</span></span>

<span data-ttu-id="1df01-294">Genera contenuto DGML (Graph) che Visualizza il DbContext.</span><span class="sxs-lookup"><span data-stu-id="1df01-294">Generate DGML (Graph) content that visualizes your DbContext.</span></span> <span data-ttu-id="1df01-295">Aggiunge il metodo di estensione AsDgml () alla classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="1df01-295">Adds the AsDgml() extension method to the DbContext class.</span></span> <span data-ttu-id="1df01-296">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-296">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-297">Wiki di GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-297">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/Inspect-your-DbContext-model)

### <a name="entityframeworkexceptions"></a><span data-ttu-id="1df01-298">EntityFramework. Exceptions</span><span class="sxs-lookup"><span data-stu-id="1df01-298">EntityFramework.Exceptions</span></span>

<span data-ttu-id="1df01-299">Quando si usa Entity Framework Core tutte le eccezioni del database vengono incapsulate in eccezione dbupdateexception.</span><span class="sxs-lookup"><span data-stu-id="1df01-299">When using Entity Framework Core all database exceptions are wrapped in DbUpdateException.</span></span> <span data-ttu-id="1df01-300">EntityFramework. Exceptions gestisce tutti i dettagli specifici del database per individuare il vincolo violato e consente di usare eccezioni tipizzate, ad esempio,,, `UniqueConstraintException` `CannotInsertNullException` `MaxLengthExceededException` `NumericOverflowException` `ReferenceConstraintException` quando la query viola i vincoli del database.</span><span class="sxs-lookup"><span data-stu-id="1df01-300">EntityFramework.Exceptions handles all the database-specific details to find which constraint was violated and allows you to use typed exceptions such as `UniqueConstraintException`, `CannotInsertNullException`, `MaxLengthExceededException`, `NumericOverflowException`, `ReferenceConstraintException` when your query violates database constraints.</span></span>

<span data-ttu-id="1df01-301">Supporta SQL Server, Postgres, MySql, SQLite e Oracle</span><span class="sxs-lookup"><span data-stu-id="1df01-301">Supports SQL Server, Postgres, MySql, SQLite and Oracle</span></span>

<span data-ttu-id="1df01-302">Per EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="1df01-302">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="1df01-303">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1df01-303">GitHub Repository</span></span>](https://github.com/Giorgi/EntityFramework.Exceptions)
