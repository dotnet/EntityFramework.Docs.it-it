---
title: Introduzione a EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7181df6ee73b09f196940178ffed38d96b075258
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370318"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="77c11-102">Introduzione a EF Core</span><span class="sxs-lookup"><span data-stu-id="77c11-102">Getting Started with EF Core</span></span>

<span data-ttu-id="77c11-103">In questa esercitazione viene creata un'app console .NET Core che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="77c11-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="77c11-104">È possibile eseguire l'esercitazione usando Visual Studio in Windows oppure usando l'interfaccia della riga di comando di .NET Core in Windows, macOS o Linux.</span><span class="sxs-lookup"><span data-stu-id="77c11-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="77c11-105">[Visualizzare l'esempio di questo articolo su GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="77c11-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77c11-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="77c11-106">Prerequisites</span></span>

<span data-ttu-id="77c11-107">Installare il software seguente:</span><span class="sxs-lookup"><span data-stu-id="77c11-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-108">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="77c11-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="77c11-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c11-111">[Visual Studio 2019 versione 16.3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:</span><span class="sxs-lookup"><span data-stu-id="77c11-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="77c11-112">**Sviluppo multipiattaforma .NET Core** (in **Altri set di strumenti**)</span><span class="sxs-lookup"><span data-stu-id="77c11-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="77c11-113">Creare un nuovo progetto</span><span class="sxs-lookup"><span data-stu-id="77c11-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-114">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c11-116">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-116">Open Visual Studio</span></span>
* <span data-ttu-id="77c11-117">Fare clic su **Crea un nuovo progetto**</span><span class="sxs-lookup"><span data-stu-id="77c11-117">Click **Create a new project**</span></span>
* <span data-ttu-id="77c11-118">Selezionare **App console (.NET Core)** con il tag **C#** e fare clic su **Avanti**</span><span class="sxs-lookup"><span data-stu-id="77c11-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="77c11-119">Immettere **EFGetStarted** come nome e fare clic su **Crea**</span><span class="sxs-lookup"><span data-stu-id="77c11-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="77c11-120">Installare Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="77c11-120">Install Entity Framework Core</span></span>

<span data-ttu-id="77c11-121">Per installare EF Core, installare il pacchetto per i provider di database di EF Core che si vuole usare come destinazione.</span><span class="sxs-lookup"><span data-stu-id="77c11-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="77c11-122">Questa esercitazione usa SQLite in quanto viene eseguita su tutte le piattaforme supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="77c11-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="77c11-123">Per un elenco dei provider disponibili, vedere [Provider di database](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="77c11-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-124">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c11-126">**Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="77c11-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="77c11-127">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="77c11-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="77c11-128">Suggerimento: È anche possibile installare i pacchetti facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Gestisci pacchetti NuGet**</span><span class="sxs-lookup"><span data-stu-id="77c11-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="77c11-129">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="77c11-129">Create the model</span></span>

<span data-ttu-id="77c11-130">Definire una classe di contesto e le classi di entità che costituiscono il modello.</span><span class="sxs-lookup"><span data-stu-id="77c11-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-131">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="77c11-132">Nella directory del progetto creare un file **Model.cs** con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="77c11-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c11-134">Fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi > Classe**</span><span class="sxs-lookup"><span data-stu-id="77c11-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="77c11-135">Immettere **Model.cs** come nome e fare clic su **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="77c11-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="77c11-136">Sostituire tutti i contenuti del file con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="77c11-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="77c11-137">EF Core supporta anche il [reverse engineering](../managing-schemas/scaffolding.md) di un modello da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="77c11-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="77c11-138">Suggerimento: questa applicazione mantiene intenzionalmente le cose semplici per maggiore chiarezza.</span><span class="sxs-lookup"><span data-stu-id="77c11-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="77c11-139">Le [stringhe di connessione](../miscellaneous/connection-strings.md) non devono essere archiviate nel codice per le applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="77c11-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="77c11-140">Potrebbe anche essere necessario suddividere ogni classe C# nel relativo file.</span><span class="sxs-lookup"><span data-stu-id="77c11-140">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="77c11-141">Creare il database</span><span class="sxs-lookup"><span data-stu-id="77c11-141">Create the database</span></span>

<span data-ttu-id="77c11-142">La procedura seguente usa le [migrazioni](xref:core/managing-schemas/migrations/index) per creare un database.</span><span class="sxs-lookup"><span data-stu-id="77c11-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-143">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="77c11-144">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="77c11-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="77c11-145">Vengono installati [dotnet ef](../miscellaneous/cli/dotnet.md) e il pacchetto di progettazione necessario per eseguire il comando in un progetto.</span><span class="sxs-lookup"><span data-stu-id="77c11-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="77c11-146">Il comando `migrations` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="77c11-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="77c11-147">Il comando `database update` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="77c11-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c11-149">Eseguire i comandi seguenti nella **console di gestione pacchetti (PMC)**</span><span class="sxs-lookup"><span data-stu-id="77c11-149">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="77c11-150">Verranno installati gli [strumenti di PMC per EF Core](../miscellaneous/cli/powershell.md).</span><span class="sxs-lookup"><span data-stu-id="77c11-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="77c11-151">Il comando `Add-Migration` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="77c11-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="77c11-152">Il comando `Update-Database` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="77c11-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="77c11-153">Creazione, lettura, aggiornamento ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="77c11-153">Create, read, update & delete</span></span>

* <span data-ttu-id="77c11-154">Aprire *Program.cs* e sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="77c11-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="77c11-155">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="77c11-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="77c11-156">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="77c11-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="77c11-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c11-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77c11-158">Visual Studio usa una directory di lavoro incoerente quando si eseguono app console .NET Core.</span><span class="sxs-lookup"><span data-stu-id="77c11-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="77c11-159">(vedere [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) Questa operazione causa la generazione di un'eccezione: *Nessuna tabella di questo tipo: Blogs*.</span><span class="sxs-lookup"><span data-stu-id="77c11-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="77c11-160">Per aggiornare la directory di lavoro:</span><span class="sxs-lookup"><span data-stu-id="77c11-160">To update the working directory:</span></span>

* <span data-ttu-id="77c11-161">Fare clic con il pulsante destro del mouse sul progetto e scegliere **Modifica file di progetto**</span><span class="sxs-lookup"><span data-stu-id="77c11-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="77c11-162">Subito dopo la proprietà *TargetFramework* aggiungere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="77c11-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="77c11-163">Salvare il file</span><span class="sxs-lookup"><span data-stu-id="77c11-163">Save the file</span></span>

<span data-ttu-id="77c11-164">A questo punto è possibile eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="77c11-164">Now you can run the app:</span></span>

* <span data-ttu-id="77c11-165">**Debug > Avvia senza eseguire debug**</span><span class="sxs-lookup"><span data-stu-id="77c11-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="77c11-166">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="77c11-166">Next steps</span></span>

* <span data-ttu-id="77c11-167">Seguire l'[esercitazione di ASP.NET Core](/aspnet/core/data/ef-rp/intro) per usare EF Core in un'app Web</span><span class="sxs-lookup"><span data-stu-id="77c11-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="77c11-168">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="77c11-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="77c11-169">[Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="77c11-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="77c11-170">Usare le [migrazioni](xref:core/managing-schemas/migrations/index) per aggiornare lo schema del database dopo aver modificato il modello</span><span class="sxs-lookup"><span data-stu-id="77c11-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
