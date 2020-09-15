---
title: Introduzione a EF Core
description: Esercitazione introduttiva a Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/index
ms.openlocfilehash: 9f0bb1eb99cb7f4cb7542c444ad86480917bdd0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071979"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="20127-103">Introduzione a EF Core</span><span class="sxs-lookup"><span data-stu-id="20127-103">Getting Started with EF Core</span></span>

<span data-ttu-id="20127-104">In questa esercitazione viene creata un'app console .NET Core che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="20127-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="20127-105">È possibile eseguire l'esercitazione usando Visual Studio in Windows oppure usando l'interfaccia della riga di comando di .NET Core in Windows, macOS o Linux.</span><span class="sxs-lookup"><span data-stu-id="20127-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="20127-106">[Visualizzare l'esempio di questo articolo su GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="20127-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="20127-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="20127-107">Prerequisites</span></span>

<span data-ttu-id="20127-108">Installare il software seguente:</span><span class="sxs-lookup"><span data-stu-id="20127-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-109">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="20127-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="20127-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="20127-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="20127-112">[Visual Studio 2019 versione 16.3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:</span><span class="sxs-lookup"><span data-stu-id="20127-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="20127-113">**Sviluppo multipiattaforma .NET Core** (in **Altri set di strumenti**)</span><span class="sxs-lookup"><span data-stu-id="20127-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="20127-114">Creare un nuovo progetto</span><span class="sxs-lookup"><span data-stu-id="20127-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-115">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="20127-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="20127-117">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-117">Open Visual Studio</span></span>
* <span data-ttu-id="20127-118">Fare clic su **Crea un nuovo progetto**</span><span class="sxs-lookup"><span data-stu-id="20127-118">Click **Create a new project**</span></span>
* <span data-ttu-id="20127-119">Selezionare **App console (.NET Core)** con il tag **C#** e fare clic su **Avanti**</span><span class="sxs-lookup"><span data-stu-id="20127-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="20127-120">Immettere **EFGetStarted** come nome e fare clic su **Crea**</span><span class="sxs-lookup"><span data-stu-id="20127-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="20127-121">Installare Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="20127-121">Install Entity Framework Core</span></span>

<span data-ttu-id="20127-122">Per installare EF Core, installare il pacchetto per i provider di database di EF Core che si vuole usare come destinazione.</span><span class="sxs-lookup"><span data-stu-id="20127-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="20127-123">Questa esercitazione usa SQLite in quanto viene eseguita su tutte le piattaforme supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="20127-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="20127-124">Per un elenco dei provider disponibili, vedere [Provider di database](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="20127-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-125">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="20127-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="20127-127">**Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="20127-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="20127-128">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="20127-128">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="20127-129">Suggerimento: È anche possibile installare i pacchetti facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Gestisci pacchetti NuGet**</span><span class="sxs-lookup"><span data-stu-id="20127-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="20127-130">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="20127-130">Create the model</span></span>

<span data-ttu-id="20127-131">Definire una classe di contesto e le classi di entità che costituiscono il modello.</span><span class="sxs-lookup"><span data-stu-id="20127-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-132">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="20127-133">Nella directory del progetto creare un file **Model.cs** con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="20127-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="20127-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="20127-135">Fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi > Classe**</span><span class="sxs-lookup"><span data-stu-id="20127-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="20127-136">Immettere **Model.cs** come nome e fare clic su **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="20127-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="20127-137">Sostituire tutti i contenuti del file con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="20127-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="20127-138">EF Core supporta anche il [reverse engineering](xref:core/managing-schemas/scaffolding) di un modello da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="20127-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="20127-139">Suggerimento: questa applicazione mantiene intenzionalmente le cose semplici per maggiore chiarezza.</span><span class="sxs-lookup"><span data-stu-id="20127-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="20127-140">Le [stringhe di connessione](xref:core/miscellaneous/connection-strings) non devono essere archiviate nel codice per le applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="20127-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="20127-141">Potrebbe anche essere preferibile suddividere ogni classe C# in un file separato.</span><span class="sxs-lookup"><span data-stu-id="20127-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="20127-142">Creare il database</span><span class="sxs-lookup"><span data-stu-id="20127-142">Create the database</span></span>

<span data-ttu-id="20127-143">La procedura seguente usa le [migrazioni](xref:core/managing-schemas/migrations/index) per creare un database.</span><span class="sxs-lookup"><span data-stu-id="20127-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="20127-145">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="20127-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="20127-146">Vengono installati [dotnet ef](xref:core/miscellaneous/cli/dotnet) e il pacchetto di progettazione necessario per eseguire il comando in un progetto.</span><span class="sxs-lookup"><span data-stu-id="20127-146">This installs [dotnet ef](xref:core/miscellaneous/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="20127-147">Il comando `migrations` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="20127-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="20127-148">Il comando `database update` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="20127-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="20127-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="20127-150">Eseguire i comandi seguenti nella **Console di Gestione pacchetti (PMC)**</span><span class="sxs-lookup"><span data-stu-id="20127-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="20127-151">Verranno installati gli [strumenti di PMC per EF Core](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="20127-151">This installs the [PMC tools for EF Core](xref:core/miscellaneous/cli/powershell).</span></span> <span data-ttu-id="20127-152">Il comando `Add-Migration` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="20127-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="20127-153">Il comando `Update-Database` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="20127-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="20127-154">Creazione, lettura, aggiornamento ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="20127-154">Create, read, update & delete</span></span>

* <span data-ttu-id="20127-155">Aprire *Program.cs* e sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="20127-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="20127-156">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="20127-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="20127-157">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="20127-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="20127-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20127-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20127-159">Visual Studio usa una directory di lavoro incoerente quando si eseguono app console .NET Core.</span><span class="sxs-lookup"><span data-stu-id="20127-159">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="20127-160">(vedere [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) Questa operazione causa la generazione di un'eccezione: *Nessuna tabella di questo tipo: Blogs*.</span><span class="sxs-lookup"><span data-stu-id="20127-160">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="20127-161">Per aggiornare la directory di lavoro:</span><span class="sxs-lookup"><span data-stu-id="20127-161">To update the working directory:</span></span>

* <span data-ttu-id="20127-162">Fare clic con il pulsante destro del mouse sul progetto e scegliere **Modifica file di progetto**</span><span class="sxs-lookup"><span data-stu-id="20127-162">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="20127-163">Subito dopo la proprietà *TargetFramework* aggiungere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="20127-163">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="20127-164">Salvare il file</span><span class="sxs-lookup"><span data-stu-id="20127-164">Save the file</span></span>

<span data-ttu-id="20127-165">A questo punto è possibile eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="20127-165">Now you can run the app:</span></span>

* <span data-ttu-id="20127-166">**Debug > Avvia senza eseguire debug**</span><span class="sxs-lookup"><span data-stu-id="20127-166">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="20127-167">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="20127-167">Next steps</span></span>

* <span data-ttu-id="20127-168">Seguire l'[esercitazione di ASP.NET Core](/aspnet/core/data/ef-rp/intro) per usare EF Core in un'app Web</span><span class="sxs-lookup"><span data-stu-id="20127-168">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="20127-169">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="20127-169">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="20127-170">[Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="20127-170">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="20127-171">Usare le [migrazioni](xref:core/managing-schemas/migrations/index) per aggiornare lo schema del database dopo aver modificato il modello</span><span class="sxs-lookup"><span data-stu-id="20127-171">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
