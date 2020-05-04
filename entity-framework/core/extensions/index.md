---
title: Strumenti ed estensioni - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 246cf687f2a087e9a9a569c875b27712ebe80c5c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538341"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="1f38e-102">Strumenti ed estensioni di EF Core</span><span class="sxs-lookup"><span data-stu-id="1f38e-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="1f38e-103">Questi strumenti ed estensioni offrono funzionalità aggiuntive per Entity Framework Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1f38e-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="1f38e-104">Le estensioni sono composte da diversi tipi di origine e non vengono mantenute nell'ambito del progetto di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="1f38e-105">Quando si prende in considerazione un'estensione di terze parti, valutarne con cura gli aspetti relativi a qualità, licenze, compatibilità, supporto e così via, per essere certi che soddisfi i propri requisiti.</span><span class="sxs-lookup"><span data-stu-id="1f38e-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="1f38e-106">In particolare, un'estensione compilata per una versione precedente di EF Core potrebbe necessitare di un aggiornamento prima che funzioni con le versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="1f38e-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="1f38e-107">Strumenti</span><span class="sxs-lookup"><span data-stu-id="1f38e-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="1f38e-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="1f38e-108">LLBLGen Pro</span></span>

<span data-ttu-id="1f38e-109">LLBLGen Pro è una soluzione per la modellazione delle entità con supporto per Entity Framework ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="1f38e-110">Consente di definire facilmente il modello di entità e di eseguirne il mapping al database, usando l'approccio Database-First o Model-First, in modo da iniziare subito a scrivere le query.</span><span class="sxs-lookup"><span data-stu-id="1f38e-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="1f38e-111">Per EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="1f38e-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="1f38e-112">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1f38e-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="1f38e-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="1f38e-113">Devart Entity Developer</span></span>

<span data-ttu-id="1f38e-114">Entity Developer è una potente finestra di progettazione ORM per ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access e LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="1f38e-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="1f38e-115">Supporta la progettazione visiva di modelli EF Core, usando l'approccio con precedenza del modello o precedenza del database e la generazione di codice C# o Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="1f38e-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="1f38e-116">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-116">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-117">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1f38e-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="1f38e-118">ORM nHydrate per Entity Framework</span><span class="sxs-lookup"><span data-stu-id="1f38e-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="1f38e-119">ORM che consente di creare classi estendibili e fortemente tipizzate per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1f38e-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="1f38e-120">Il codice generato è Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="1f38e-121">Non c'è alcuna differenza.</span><span class="sxs-lookup"><span data-stu-id="1f38e-121">There is no difference.</span></span> <span data-ttu-id="1f38e-122">Non si tratta di una sostituzione per EF o di un ORM personalizzato.</span><span class="sxs-lookup"><span data-stu-id="1f38e-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="1f38e-123">Si tratta di un livello di modellazione visivo che consente a un team di gestire schemi di database complessi.</span><span class="sxs-lookup"><span data-stu-id="1f38e-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="1f38e-124">Funziona bene con software SCM come Git, consentendo l'accesso multiutente al modello con conflitti minimi.</span><span class="sxs-lookup"><span data-stu-id="1f38e-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="1f38e-125">Il programma di installazione tiene traccia delle modifiche del modello e crea gli script di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="1f38e-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="1f38e-126">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-126">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-127">Sito GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="1f38e-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="1f38e-128">EF Core Power Tools</span></span>

<span data-ttu-id="1f38e-129">EF Core Power Tools è un'estensione di Visual Studio che espone varie attività di progettazione di EF Core in un'interfaccia utente intuitiva.</span><span class="sxs-lookup"><span data-stu-id="1f38e-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="1f38e-130">Include la decompilazione di classi DbContext e classi di entità da database esistenti e [pacchetti di applicazione livello dati SQL Server](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), la gestione delle migrazioni del database e le visualizzazioni del modello.</span><span class="sxs-lookup"><span data-stu-id="1f38e-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="1f38e-131">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-132">Wiki di GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="1f38e-133">Entity Framework Visual Editor</span><span class="sxs-lookup"><span data-stu-id="1f38e-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="1f38e-134">Entity Framework Visual Editor è un'estensione di Visual Studio che aggiunge una finestra di progettazione ORM per la progettazione visiva di classi di Entity Framework 6 ed EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="1f38e-135">Il codice viene generato usando i modelli T4, pertanto può essere personalizzato per soddisfare qualsiasi esigenza.</span><span class="sxs-lookup"><span data-stu-id="1f38e-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="1f38e-136">Supporta l'ereditarietà, le associazioni unidirezionali e bidirezionali, le enumerazioni e la possibilità di usare una codifica a colori per le classi e di aggiungere blocchi di testo, per spiegare parti potenzialmente molto complesse del progetto.</span><span class="sxs-lookup"><span data-stu-id="1f38e-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="1f38e-137">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-137">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="1f38e-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="1f38e-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="1f38e-139">CatFactory</span></span>

<span data-ttu-id="1f38e-140">CatFactory è un motore di scaffolding per .NET Core in grado di automatizzare la generazione di classi, entità, configurazioni di mapping e classi repository DbContext da un database SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f38e-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="1f38e-141">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-141">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-142">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="1f38e-143">Generatore Entity Framework Core di LoreSoft</span><span class="sxs-lookup"><span data-stu-id="1f38e-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="1f38e-144">Il Generatore Entity Framework Core (efg) è uno strumento della riga di comando di .NET Core che genera modelli EF Core da un database esistente con modalità simili a `dotnet ef dbcontext scaffold`, ma supporta anche la [rigenerazione](https://efg.loresoft.com/en/latest/regeneration/) del codice sicuro tramite la sostituzione dell'area o tramite l'analisi dei file di mapping.</span><span class="sxs-lookup"><span data-stu-id="1f38e-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="1f38e-145">Lo strumento supporta la generazione di modelli di visualizzazione, la convalida e il codice mapper degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="1f38e-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="1f38e-146">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-146">For EF Core: 2.</span></span>

<span data-ttu-id="1f38e-147">[Esercitazione](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentazione](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="1f38e-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="1f38e-148">Estensioni</span><span class="sxs-lookup"><span data-stu-id="1f38e-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="1f38e-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="1f38e-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="1f38e-150">Libreria di plug-in che consente di registrare automaticamente le modifiche ai dati eseguite da EF Core in una tabella di cronologia.</span><span class="sxs-lookup"><span data-stu-id="1f38e-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="1f38e-151">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-151">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-152">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="1f38e-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="1f38e-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="1f38e-154">La memorizzazione nella cache di secondo livello è una cache della query.</span><span class="sxs-lookup"><span data-stu-id="1f38e-154">Second level caching is a query cache.</span></span> <span data-ttu-id="1f38e-155">I risultati dei comandi EF verranno memorizzati nella cache, in modo che gli stessi comandi EF recupereranno i dati dalla cache anziché eseguirli di nuovo sul database.</span><span class="sxs-lookup"><span data-stu-id="1f38e-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="1f38e-156">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-156">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-157">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="1f38e-158">Geco</span><span class="sxs-lookup"><span data-stu-id="1f38e-158">Geco</span></span>

<span data-ttu-id="1f38e-159">Geco (Generator Console) è un generatore di codice semplice basato su un progetto di console, che viene eseguito in .NET Core e usa stringhe interpolate C# per la generazione di codice.</span><span class="sxs-lookup"><span data-stu-id="1f38e-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="1f38e-160">Geco include un generatore di modelli inversi per EF Core con supporto di pluralizzazione, singolarizzazione e modelli modificabili.</span><span class="sxs-lookup"><span data-stu-id="1f38e-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="1f38e-161">Include anche un generatore di script per dati di inizializzazione e uno strumento di pulizia database.</span><span class="sxs-lookup"><span data-stu-id="1f38e-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="1f38e-162">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-162">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-163">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="1f38e-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="1f38e-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="1f38e-165">Consente la personalizzazione di classi decompilate da un database esistente usando la toolchain di Entity Framework Core con modelli Handlebars.</span><span class="sxs-lookup"><span data-stu-id="1f38e-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="1f38e-166">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-167">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="1f38e-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1f38e-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="1f38e-169">NeinLinq estende i provider LINQ, ad esempio Entity Framework, e consente il riuso delle funzioni, la riscrittura di query e la creazione di query dinamiche usando predicati e selettori traducibili.</span><span class="sxs-lookup"><span data-stu-id="1f38e-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="1f38e-170">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-171">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="1f38e-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="1f38e-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="1f38e-173">Plug-in per Microsoft.EntityFrameworkCore che supporta repository, modelli di unità di lavoro e più database con la transazione distribuita supportata.</span><span class="sxs-lookup"><span data-stu-id="1f38e-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="1f38e-174">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-174">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-175">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="1f38e-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="1f38e-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="1f38e-177">Estensioni EF Core per operazioni in blocco (inserimento, aggiornamento, eliminazione).</span><span class="sxs-lookup"><span data-stu-id="1f38e-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="1f38e-178">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-179">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="1f38e-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="1f38e-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="1f38e-181">Aggiunge la pluralizzazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="1f38e-181">Adds design-time pluralization.</span></span> <span data-ttu-id="1f38e-182">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-182">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-183">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="1f38e-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="1f38e-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="1f38e-185">Ripresa dell'attributo [Index] (con estensione per la compilazione del modello).</span><span class="sxs-lookup"><span data-stu-id="1f38e-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="1f38e-186">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-187">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="1f38e-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="1f38e-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="1f38e-189">Definisce un wrapper per il Provider di database in memoria EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="1f38e-190">Ne rende il funzionamento più simile a quello di un provider relazionale.</span><span class="sxs-lookup"><span data-stu-id="1f38e-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="1f38e-191">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-191">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-192">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="1f38e-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="1f38e-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="1f38e-194">Implementazione di supporto temporale.</span><span class="sxs-lookup"><span data-stu-id="1f38e-194">An implementation of temporal support.</span></span> <span data-ttu-id="1f38e-195">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-195">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-196">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="1f38e-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="1f38e-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="1f38e-198">Consente di eseguire facilmente query temporali nel database preferito usando metodi di estensione introdotti: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="1f38e-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="1f38e-199">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-199">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-200">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="1f38e-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="1f38e-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="1f38e-202">Consente query Entity Framework Core complete sulla [cronologia temporale di SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) usando il codice, le entità e i mapping di EF Core già definiti.</span><span class="sxs-lookup"><span data-stu-id="1f38e-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="1f38e-203">Consente lo spostamento cronologico tramite il wrapping del codice in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span><span class="sxs-lookup"><span data-stu-id="1f38e-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="1f38e-204">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-204">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-205">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="1f38e-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="1f38e-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="1f38e-207">Libreria di estensioni per Entity Framework Core che consente agli sviluppatori che usano SQL Server di usare facilmente le tabelle temporali.</span><span class="sxs-lookup"><span data-stu-id="1f38e-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="1f38e-208">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-208">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-209">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="1f38e-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="1f38e-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="1f38e-211">Cache della query di secondo livello ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="1f38e-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="1f38e-212">Per EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="1f38e-212">For EF Core: 2.</span></span>

[<span data-ttu-id="1f38e-213">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="1f38e-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="1f38e-214">Entity Framework Plus</span></span>

<span data-ttu-id="1f38e-215">Estende DbContext con funzionalità quali: filtro di inclusione, controllo, memorizzazione nella cache, query Future, eliminazione in batch, aggiornamento in batch e molte altre.</span><span class="sxs-lookup"><span data-stu-id="1f38e-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="1f38e-216">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="1f38e-217">[Sito Web](https://entityframework-plus.net/)
[Repository GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="1f38e-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="1f38e-218">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="1f38e-218">Entity Framework Extensions</span></span>

<span data-ttu-id="1f38e-219">Estende DbContext con operazioni in blocco ad alte prestazioni: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge e altre.</span><span class="sxs-lookup"><span data-stu-id="1f38e-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="1f38e-220">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-221">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1f38e-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="1f38e-222">Expressionify</span><span class="sxs-lookup"><span data-stu-id="1f38e-222">Expressionify</span></span>

<span data-ttu-id="1f38e-223">Aggiunta del supporto per la chiamata di metodi di estensione nelle espressioni lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="1f38e-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="1f38e-224">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-224">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-225">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="1f38e-226">XLinq</span><span class="sxs-lookup"><span data-stu-id="1f38e-226">XLinq</span></span>

<span data-ttu-id="1f38e-227">Tecnologia LINQ (Language Integrated Query) per database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1f38e-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="1f38e-228">Consente di usare C# per scrivere query fortemente tipizzate.</span><span class="sxs-lookup"><span data-stu-id="1f38e-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="1f38e-229">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-229">For EF Core: 3.</span></span>

- <span data-ttu-id="1f38e-230">Supporto C# completo per la creazione di query: più istruzioni all'interno di lambda, variabili, funzioni e così via.</span><span class="sxs-lookup"><span data-stu-id="1f38e-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="1f38e-231">Nessun gap semantico con SQL.</span><span class="sxs-lookup"><span data-stu-id="1f38e-231">No semantic gap with SQL.</span></span> <span data-ttu-id="1f38e-232">XLinq dichiara istruzioni SQL (ad esempio `SELECT`, `FROM`, `WHERE`) come metodi C# di prima classe, combinando la sintassi familiare con IntelliSense, l'indipendenza dai tipi e il refactoring.</span><span class="sxs-lookup"><span data-stu-id="1f38e-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="1f38e-233">Di conseguenza, SQL diventa semplicemente "un'altra" libreria di classi che espone l'API in locale, letteralmente *"SQL integrato nel linguaggio"* .</span><span class="sxs-lookup"><span data-stu-id="1f38e-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="1f38e-234">Sito Web</span><span class="sxs-lookup"><span data-stu-id="1f38e-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="1f38e-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="1f38e-235">Ramses</span></span>

<span data-ttu-id="1f38e-236">Hook del ciclo di vita (per SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="1f38e-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="1f38e-237">Per EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="1f38e-238">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="1f38e-239">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="1f38e-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="1f38e-240">Per tutti i nomi di tabella e colonna verrà automaticamente usata la convenzione di denominazione snake_case, tutte maiuscole o tutte minuscole.</span><span class="sxs-lookup"><span data-stu-id="1f38e-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="1f38e-241">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-241">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-242">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="1f38e-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="1f38e-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="1f38e-244">Aggiunge il supporto nativo a EntityFrameworkCore per SQL Server per i tipi NodaTime.</span><span class="sxs-lookup"><span data-stu-id="1f38e-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="1f38e-245">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-245">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-246">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="1f38e-247">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="1f38e-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="1f38e-248">Estensioni LINQ per Entity Framework Core 3.1 per supportare le query di tabelle temporali di Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f38e-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="1f38e-249">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-249">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-250">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="1f38e-251">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="1f38e-251">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="1f38e-252">Aggiunge il supporto di hierarchyid al provider SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f38e-252">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="1f38e-253">Per EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="1f38e-253">For EF Core: 3.</span></span>

[<span data-ttu-id="1f38e-254">Repository GitHub</span><span class="sxs-lookup"><span data-stu-id="1f38e-254">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)
