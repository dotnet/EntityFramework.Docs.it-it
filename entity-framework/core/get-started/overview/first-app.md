---
title: Introduzione a EF Core
description: Esercitazione introduttiva a Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: 560a6f293e32f4e1f75c13367291c4e31e83b157
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024342"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="8f09b-103">Introduzione a EF Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-103">Getting Started with EF Core</span></span>

<span data-ttu-id="8f09b-104">In questa esercitazione viene creata un'app console .NET Core che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8f09b-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="8f09b-105">È possibile eseguire l'esercitazione usando Visual Studio in Windows oppure usando l'interfaccia della riga di comando di .NET Core in Windows, macOS o Linux.</span><span class="sxs-lookup"><span data-stu-id="8f09b-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="8f09b-106">[Visualizzare l'esempio di questo articolo su GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="8f09b-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8f09b-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="8f09b-107">Prerequisites</span></span>

<span data-ttu-id="8f09b-108">Installare il software seguente:</span><span class="sxs-lookup"><span data-stu-id="8f09b-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-109">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8f09b-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="8f09b-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8f09b-112">[Visual Studio 2019 versione 16.3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:</span><span class="sxs-lookup"><span data-stu-id="8f09b-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="8f09b-113">**Sviluppo multipiattaforma .NET Core** (in **Altri set di strumenti**)</span><span class="sxs-lookup"><span data-stu-id="8f09b-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="8f09b-114">Creare un nuovo progetto</span><span class="sxs-lookup"><span data-stu-id="8f09b-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-115">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8f09b-117">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-117">Open Visual Studio</span></span>
* <span data-ttu-id="8f09b-118">Fare clic su **Crea un nuovo progetto**</span><span class="sxs-lookup"><span data-stu-id="8f09b-118">Click **Create a new project**</span></span>
* <span data-ttu-id="8f09b-119">Selezionare **App console (.NET Core)** con il tag **C#** e fare clic su **Avanti**</span><span class="sxs-lookup"><span data-stu-id="8f09b-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="8f09b-120">Immettere **EFGetStarted** come nome e fare clic su **Crea**</span><span class="sxs-lookup"><span data-stu-id="8f09b-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="8f09b-121">Installare Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-121">Install Entity Framework Core</span></span>

<span data-ttu-id="8f09b-122">Per installare EF Core, installare il pacchetto per i provider di database di EF Core che si vuole usare come destinazione.</span><span class="sxs-lookup"><span data-stu-id="8f09b-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="8f09b-123">Questa esercitazione usa SQLite in quanto viene eseguita su tutte le piattaforme supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f09b-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="8f09b-124">Per un elenco dei provider disponibili, vedere [Provider di database](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="8f09b-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-125">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8f09b-127">**Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="8f09b-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="8f09b-128">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8f09b-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="8f09b-129">Suggerimento: È anche possibile installare i pacchetti facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Gestisci pacchetti NuGet**</span><span class="sxs-lookup"><span data-stu-id="8f09b-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="8f09b-130">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="8f09b-130">Create the model</span></span>

<span data-ttu-id="8f09b-131">Definire una classe di contesto e le classi di entità che costituiscono il modello.</span><span class="sxs-lookup"><span data-stu-id="8f09b-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-132">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8f09b-133">Nella directory del progetto creare un file **Model.cs** con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="8f09b-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8f09b-135">Fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi > Classe**</span><span class="sxs-lookup"><span data-stu-id="8f09b-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="8f09b-136">Immettere **Model.cs** come nome e fare clic su **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="8f09b-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="8f09b-137">Sostituire tutti i contenuti del file con il codice seguente</span><span class="sxs-lookup"><span data-stu-id="8f09b-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="8f09b-138">EF Core supporta anche il [reverse engineering](xref:core/managing-schemas/scaffolding) di un modello da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="8f09b-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="8f09b-139">Suggerimento: questa applicazione mantiene intenzionalmente le cose semplici per maggiore chiarezza.</span><span class="sxs-lookup"><span data-stu-id="8f09b-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="8f09b-140">Le [stringhe di connessione](xref:core/miscellaneous/connection-strings) non devono essere archiviate nel codice per le applicazioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="8f09b-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="8f09b-141">Potrebbe anche essere preferibile suddividere ogni classe C# in un file separato.</span><span class="sxs-lookup"><span data-stu-id="8f09b-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="8f09b-142">Creare il database</span><span class="sxs-lookup"><span data-stu-id="8f09b-142">Create the database</span></span>

<span data-ttu-id="8f09b-143">La procedura seguente usa le [migrazioni](xref:core/managing-schemas/migrations/index) per creare un database.</span><span class="sxs-lookup"><span data-stu-id="8f09b-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8f09b-145">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8f09b-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="8f09b-146">Vengono installati [dotnet ef](xref:core/cli/dotnet) e il pacchetto di progettazione necessario per eseguire il comando in un progetto.</span><span class="sxs-lookup"><span data-stu-id="8f09b-146">This installs [dotnet ef](xref:core/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="8f09b-147">Il comando `migrations` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="8f09b-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="8f09b-148">Il comando `database update` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="8f09b-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8f09b-150">Eseguire i comandi seguenti nella **Console di Gestione pacchetti (PMC)**</span><span class="sxs-lookup"><span data-stu-id="8f09b-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="8f09b-151">Verranno installati gli [strumenti di PMC per EF Core](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="8f09b-151">This installs the [PMC tools for EF Core](xref:core/cli/powershell).</span></span> <span data-ttu-id="8f09b-152">Il comando `Add-Migration` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello.</span><span class="sxs-lookup"><span data-stu-id="8f09b-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="8f09b-153">Il comando `Update-Database` crea il database e ne applica la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="8f09b-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="8f09b-154">Creazione, lettura, aggiornamento ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="8f09b-154">Create, read, update & delete</span></span>

* <span data-ttu-id="8f09b-155">Aprire *Program.cs* e sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8f09b-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="8f09b-156">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="8f09b-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8f09b-157">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f09b-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="8f09b-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f09b-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8f09b-159">**Debug > Avvia senza eseguire debug**</span><span class="sxs-lookup"><span data-stu-id="8f09b-159">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="8f09b-160">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="8f09b-160">Next steps</span></span>

* <span data-ttu-id="8f09b-161">Seguire l'[esercitazione di ASP.NET Core](/aspnet/core/data/ef-rp/intro) per usare EF Core in un'app Web</span><span class="sxs-lookup"><span data-stu-id="8f09b-161">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="8f09b-162">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="8f09b-162">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="8f09b-163">[Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="8f09b-163">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="8f09b-164">Usare le [migrazioni](xref:core/managing-schemas/migrations/index) per aggiornare lo schema del database dopo aver modificato il modello</span><span class="sxs-lookup"><span data-stu-id="8f09b-164">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
