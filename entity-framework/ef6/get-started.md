---
title: Introduzione a Entity Framework 6 - EF6
description: Introduzione a Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062789"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="2a614-103">Introduzione a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="2a614-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="2a614-104">Questa guida contiene una raccolta di collegamenti ad articoli della documentazione, procedure dettagliate e video selezionati per iniziare a usare rapidamente il prodotto.</span><span class="sxs-lookup"><span data-stu-id="2a614-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="2a614-105">Nozioni di base</span><span class="sxs-lookup"><span data-stu-id="2a614-105">Fundamentals</span></span>

* [<span data-ttu-id="2a614-106">Ottenere Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2a614-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="2a614-107">Viene descritto come aggiungere Entity Framework alle applicazioni e, se si vuole usare EF Designer, assicurarsi che venga installato in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2a614-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="2a614-108">Creazione di un modello: flussi di lavoro di Code First, EF Designer ed EF</span><span class="sxs-lookup"><span data-stu-id="2a614-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="2a614-109">Si preferisce specificare il modello EF scrivendo codice o tracciando caselle e linee?</span><span class="sxs-lookup"><span data-stu-id="2a614-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="2a614-110">Si userà EF per mappare gli oggetti in un database esistente o si vuole che EF crei un database specifico per gli oggetti?</span><span class="sxs-lookup"><span data-stu-id="2a614-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="2a614-111">Di seguito vengono illustrati due diversi approcci per l'uso di EF6: EF designer e Code First.</span><span class="sxs-lookup"><span data-stu-id="2a614-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="2a614-112">Seguire la discussione e guardare il video che illustra le differenze.</span><span class="sxs-lookup"><span data-stu-id="2a614-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="2a614-113">Utilizzo di DbContext</span><span class="sxs-lookup"><span data-stu-id="2a614-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="2a614-114">DbContext è il principale tipo di Entity Framework che è necessario imparare a usare.</span><span class="sxs-lookup"><span data-stu-id="2a614-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="2a614-115">Svolge la funzione di launchpad delle query del database e tiene traccia delle modifiche apportate agli oggetti in modo che possano essere salvate in modo permanente nel database.</span><span class="sxs-lookup"><span data-stu-id="2a614-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="2a614-116">Invia una domanda</span><span class="sxs-lookup"><span data-stu-id="2a614-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="2a614-117">È possibile ricevere assistenza dagli esperti e contribuire con le proprie risposte alla community.</span><span class="sxs-lookup"><span data-stu-id="2a614-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="2a614-118">Collabora</span><span class="sxs-lookup"><span data-stu-id="2a614-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="2a614-119">Entity Framework 6 usa un modello di sviluppo aperto.</span><span class="sxs-lookup"><span data-stu-id="2a614-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="2a614-120">Nel repository GitHub sono disponibili informazioni su come migliorare ulteriormente Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2a614-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="2a614-121">Risorse per Code First</span><span class="sxs-lookup"><span data-stu-id="2a614-121">Code First resources</span></span>

  - [<span data-ttu-id="2a614-122">Code First nel flusso di lavoro di un database esistente</span><span class="sxs-lookup"><span data-stu-id="2a614-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="2a614-123">Code First nel flusso di lavoro di un nuovo database</span><span class="sxs-lookup"><span data-stu-id="2a614-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="2a614-124">Mapping delle enumerazioni usando Code First</span><span class="sxs-lookup"><span data-stu-id="2a614-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="2a614-125">Mapping dei tipi spaziali usando Code First</span><span class="sxs-lookup"><span data-stu-id="2a614-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="2a614-126">Creazione di convenzioni Code First personalizzate</span><span class="sxs-lookup"><span data-stu-id="2a614-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="2a614-127">Uso della configurazione Fluent di Code First con Visual Basic</span><span class="sxs-lookup"><span data-stu-id="2a614-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="2a614-128">Migrazioni Code First</span><span class="sxs-lookup"><span data-stu-id="2a614-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="2a614-129">Migrazioni Code First in ambienti team</span><span class="sxs-lookup"><span data-stu-id="2a614-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="2a614-130">[Migrazioni Code First automatiche](xref:ef6/modeling/code-first/migrations/automatic) (non consigliato)</span><span class="sxs-lookup"><span data-stu-id="2a614-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="2a614-131">Risorse per EF Designer</span><span class="sxs-lookup"><span data-stu-id="2a614-131">EF Designer resources</span></span>
  - [<span data-ttu-id="2a614-132">Flusso di lavoro di Database First</span><span class="sxs-lookup"><span data-stu-id="2a614-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="2a614-133">Flusso di lavoro di Model First</span><span class="sxs-lookup"><span data-stu-id="2a614-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="2a614-134">Mapping delle enumerazioni</span><span class="sxs-lookup"><span data-stu-id="2a614-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="2a614-135">Mapping dei tipi spaziali</span><span class="sxs-lookup"><span data-stu-id="2a614-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="2a614-136">Mapping dell'ereditarietà della tabella per gerarchia</span><span class="sxs-lookup"><span data-stu-id="2a614-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="2a614-137">Mapping dell'ereditarietà della tabella per tipo</span><span class="sxs-lookup"><span data-stu-id="2a614-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="2a614-138">Mapping di stored procedure per gli aggiornamenti</span><span class="sxs-lookup"><span data-stu-id="2a614-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="2a614-139">Mapping di stored procedure per le query</span><span class="sxs-lookup"><span data-stu-id="2a614-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="2a614-140">Suddivisione di entità</span><span class="sxs-lookup"><span data-stu-id="2a614-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="2a614-141">Suddivisione di tabelle</span><span class="sxs-lookup"><span data-stu-id="2a614-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="2a614-142">[Query di definizione](xref:ef6/modeling/designer/advanced/defining-query) (informazioni avanzate)</span><span class="sxs-lookup"><span data-stu-id="2a614-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="2a614-143">[Funzioni con valori di tabella](xref:ef6/modeling/designer/advanced/tvfs) (informazioni avanzate)</span><span class="sxs-lookup"><span data-stu-id="2a614-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="2a614-144">Altre risorse</span><span class="sxs-lookup"><span data-stu-id="2a614-144">Other resources</span></span>
  - [<span data-ttu-id="2a614-145">Query e salvataggio asincroni</span><span class="sxs-lookup"><span data-stu-id="2a614-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="2a614-146">Data binding con WinForms</span><span class="sxs-lookup"><span data-stu-id="2a614-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="2a614-147">Data binding con WPF</span><span class="sxs-lookup"><span data-stu-id="2a614-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="2a614-148">[Scenari disconnessi con entità con rilevamento automatico](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (non consigliato)</span><span class="sxs-lookup"><span data-stu-id="2a614-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
