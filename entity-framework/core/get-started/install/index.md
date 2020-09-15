---
title: Installazione di Entity Framework Core - EF Core
description: Istruzioni di installazione per Entity Framework Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 1cc6f8f73739e61adefb375ee37a830933903470
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617989"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="4ac60-103">Installazione di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4ac60-104">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="4ac60-104">Prerequisites</span></span>

* <span data-ttu-id="4ac60-105">EF Core è una libreria [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="4ac60-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="4ac60-106">Pertanto, EF Core richiede un'implementazione di .NET che supporta .NET Standard 2.0 per l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="4ac60-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="4ac60-107">EF Core può anche essere indicato come riferimento da altre librerie .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="4ac60-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="4ac60-108">Ad esempio, è possibile usare EF Core per sviluppare app la cui destinazione è .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="4ac60-109">La compilazione di app .NET Core richiede [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="4ac60-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="4ac60-110">Facoltativamente, è anche possibile usare un ambiente di sviluppo come [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac), o [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="4ac60-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="4ac60-111">Per altre informazioni, vedere [Introduzione a .NET Core](/dotnet/core/get-started).</span><span class="sxs-lookup"><span data-stu-id="4ac60-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="4ac60-112">È possibile usare EF Core per lo sviluppo di applicazioni in Windows con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ac60-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="4ac60-113">È consigliata l'ultima versione di [Visual Studio](https://visualstudio.microsoft.com/vs).</span><span class="sxs-lookup"><span data-stu-id="4ac60-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="4ac60-114">EF Core può essere eseguito in altre implementazioni di .NET come [Xamarin](https://dotnet.microsoft.com/apps/xamarin) e .NET Native,</span><span class="sxs-lookup"><span data-stu-id="4ac60-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="4ac60-115">tuttavia in pratica tali implementazioni presentano delle limitazioni di runtime che potrebbero incidere sull'efficacia di EF Core sulla propria app.</span><span class="sxs-lookup"><span data-stu-id="4ac60-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="4ac60-116">Per altre informazioni, vedere [.Implementazioni di .NET supportate da EF Core](xref:core/platforms/index).</span><span class="sxs-lookup"><span data-stu-id="4ac60-116">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="4ac60-117">Infine, diversi provider di database potrebbero richiedere versioni del motore di database, implementazioni di .NET o sistemi operativi specifici.</span><span class="sxs-lookup"><span data-stu-id="4ac60-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="4ac60-118">Assicurarsi che sia disponibile un [provider di database EF Core](xref:core/providers/index) che supporta l'ambiente corretto per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="4ac60-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="4ac60-119">Ottenere il runtime Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="4ac60-120">Per aggiungere EF Core a un'applicazione, installare il pacchetto NuGet del provider di database da usare.</span><span class="sxs-lookup"><span data-stu-id="4ac60-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="4ac60-121">Se si sta creando un'applicazione ASP.NET Core, non è necessario installare i provider interni alla memoria e quelli di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4ac60-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="4ac60-122">Tali provider sono inclusi nelle versioni correnti di ASP.NET Core, insieme al runtime di EF Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>  

<span data-ttu-id="4ac60-123">Per installare o aggiornare i pacchetti NuGet, è possibile usare l'interfaccia della riga di comando di .NET Core, la finestra di dialogo Gestione pacchetti di Visual Studio o la console di Gestione pacchetti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ac60-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="4ac60-124">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-124">.NET Core CLI</span></span>

* <span data-ttu-id="4ac60-125">Usare il comando di interfaccia della riga di comando di .NET Core seguente dalla riga di comando del sistema operativo per installare o aggiornare il provider di EF Core SQL Server:</span><span class="sxs-lookup"><span data-stu-id="4ac60-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="4ac60-126">È possibile indicare una versione specifica nel comando `dotnet add package` mediante il modificatore `-v`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="4ac60-127">Ad esempio, per installare i pacchetti di EF Core 2.2.0, aggiungere `-v 2.2.0` al comando.</span><span class="sxs-lookup"><span data-stu-id="4ac60-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="4ac60-128">Per altre informazioni, vedere [Strumenti dell'interfaccia della riga di comando (CLI) di .NET ](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="4ac60-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="4ac60-129">Finestra di dialogo Gestione pacchetti NuGet in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ac60-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="4ac60-130">Nel menu Visual Studio, selezionare **Progetto > Gestisci pacchetti NuGet**</span><span class="sxs-lookup"><span data-stu-id="4ac60-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="4ac60-131">Fare clic sulla scheda **Sfoglia** o sulla scheda **Aggiornamenti**</span><span class="sxs-lookup"><span data-stu-id="4ac60-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="4ac60-132">Per installare o aggiornare il provider SQL Server selezionare il pacchetto `Microsoft.EntityFrameworkCore.SqlServer` e confermare.</span><span class="sxs-lookup"><span data-stu-id="4ac60-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="4ac60-133">Per altre informazioni, vedere [Finestra di dialogo Gestione pacchetti NuGet](/nuget/tools/package-manager-ui).</span><span class="sxs-lookup"><span data-stu-id="4ac60-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="4ac60-134">Console di Gestione pacchetti NuGet di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ac60-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="4ac60-135">Nel menu Visual Studio, selezionare **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="4ac60-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="4ac60-136">Per installare il provider SQL Server eseguire il comando seguente nella console di Gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="4ac60-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="4ac60-137">Per aggiornare il provider, usare il comando `Update-Package`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="4ac60-138">Per indicare una versione specifica usare il modificatore `-Version`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="4ac60-139">Ad esempio, per installare i pacchetti di EF Core 2.2.0 aggiungere `-Version 2.2.0` ai comandi</span><span class="sxs-lookup"><span data-stu-id="4ac60-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="4ac60-140">Per altre informazioni, vedere [Console di Gestione pacchetti](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="4ac60-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="4ac60-141">Ottenere gli strumenti di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="4ac60-142">È possibile installare gli strumenti per eseguire attività associate a EF Core nel progetto, come creare e applicare le migrazioni del database o creare un modello di EF Core basato su un database esistente.</span><span class="sxs-lookup"><span data-stu-id="4ac60-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="4ac60-143">Sono disponibili due set di strumenti:</span><span class="sxs-lookup"><span data-stu-id="4ac60-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="4ac60-144">Gli [strumenti dell'interfaccia della riga di comando (CLI) di .NET Core](xref:core/miscellaneous/cli/dotnet) possono essere usati in Windows, Linux o macOS.</span><span class="sxs-lookup"><span data-stu-id="4ac60-144">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="4ac60-145">Questi comandi iniziano con `dotnet ef`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="4ac60-146">Gli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell) vengono eseguiti in Visual Studio su Windows.</span><span class="sxs-lookup"><span data-stu-id="4ac60-146">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="4ac60-147">Questi comandi iniziano con un verbo, ad esempio `Add-Migration`, `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="4ac60-148">Sebbene sia possibile usare anche i comandi `dotnet ef` della console di Gestione pacchetti, quando si usa Visual Studio è consigliabile usare gli strumenti della console di Gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="4ac60-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="4ac60-149">i comandi funzionano automaticamente con il progetto corrente selezionato nella Console di Gestione pacchetti in Visual Studio, senza richiedere il passaggio manuale da una directory all'altra.</span><span class="sxs-lookup"><span data-stu-id="4ac60-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>  

* <span data-ttu-id="4ac60-150">Dopo il completamento del comando, i file generati vengono aperti automaticamente in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ac60-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="4ac60-151">Ottenere gli strumenti dell'interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="4ac60-152">Gli strumenti CLI di .NET core richiedono .NET Core SDK, indicato in precedenza nei [Prerequisiti](#prerequisites).</span><span class="sxs-lookup"><span data-stu-id="4ac60-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

<span data-ttu-id="4ac60-153">I comandi `dotnet ef` sono inclusi nelle versioni correnti di .NET Core SDK, ma per abilitarli su un progetto specifico è necessario installare il pacchetto `Microsoft.EntityFrameworkCore.Design`:</span><span class="sxs-lookup"><span data-stu-id="4ac60-153">The `dotnet ef` commands are included in current versions of the .NET Core SDK, but to enable the commands on a specific project, you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]
> <span data-ttu-id="4ac60-154">Usare sempre le versioni dei pacchetti degli strumenti corrispondenti alla versione principale dei pacchetti di runtime.</span><span class="sxs-lookup"><span data-stu-id="4ac60-154">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="4ac60-155">Ottenere gli strumenti della console di Gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="4ac60-155">Get the Package Manager Console tools</span></span>

<span data-ttu-id="4ac60-156">Per ottenere gli strumenti della console di Gestione pacchetti per EF Core, installare il pacchetto `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="4ac60-156">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="4ac60-157">Ad esempio, in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4ac60-157">For example, from Visual Studio:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="4ac60-158">Per le app ASP.NET Core, questo pacchetto è incluso automaticamente.</span><span class="sxs-lookup"><span data-stu-id="4ac60-158">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="4ac60-159">Aggiornamento alla versione più recente di EF Core</span><span class="sxs-lookup"><span data-stu-id="4ac60-159">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="4ac60-160">Ogni volta che viene rilasciata una nuova versione di EF Core, viene rilasciata anche una nuova versione dei provider che fanno parte del progetto EF Core, come Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite e Microsoft.EntityFrameworkCore.InMemory.</span><span class="sxs-lookup"><span data-stu-id="4ac60-160">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="4ac60-161">È possibile eseguire soltanto l'aggiornamento alla nuova versione del provider per ottenere tutti i miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="4ac60-161">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="4ac60-162">EF Core, insieme a SQL Server e ai provider interni alla memoria sono inclusi nelle versioni correnti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-162">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="4ac60-163">Per aggiornare un'applicazione ASP.NET Core esistente a una versione più recente di EF Core, eseguire sempre l'aggiornamento della versione di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-163">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="4ac60-164">Se si deve aggiornare un'applicazione che usa un provider di database di terze parti, verificare sempre se è disponibile un aggiornamento del provider compatibile con la versione di Entity Framework Core che si vuole usare.</span><span class="sxs-lookup"><span data-stu-id="4ac60-164">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="4ac60-165">Ad esempio, i provider di database per le versioni precedenti non sono compatibili con la versione 2.0 del runtime di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-165">For example, database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="4ac60-166">I provider di terze parti per EF Core in genere non rilasciano versioni di patch insieme al runtime di EF Core.</span><span class="sxs-lookup"><span data-stu-id="4ac60-166">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="4ac60-167">Per aggiornare un'applicazione che usa un provider di terze parti a una versione patch di EF Core, potrebbe essere necessario aggiungere un riferimento diretto ai singoli componenti di runtime di EF Core, quali Microsoft.EntityFrameworkCore, e Microsoft.EntityFrameworkCore.Relational.</span><span class="sxs-lookup"><span data-stu-id="4ac60-167">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>

* <span data-ttu-id="4ac60-168">Se si aggiorna un'applicazione esistente alla versione più recente di EF Core, potrebbe essere necessario rimuovere manualmente alcuni riferimenti a pacchetti EF Core meno recenti:</span><span class="sxs-lookup"><span data-stu-id="4ac60-168">If you're upgrading an existing application to the latest version of EF Core, some references to older EF Core packages may need to be removed manually:</span></span>

  * <span data-ttu-id="4ac60-169">I pacchetti della fase di progettazione per provider di database quali `Microsoft.EntityFrameworkCore.SqlServer.Design` non sono più necessari né supportati da EF Core 2.0 e versioni successive, ma non vengono rimossi automaticamente quando si aggiornano gli altri pacchetti.</span><span class="sxs-lookup"><span data-stu-id="4ac60-169">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported from EF Core 2.0 and later, but aren't automatically removed when upgrading the other packages.</span></span>

  * <span data-ttu-id="4ac60-170">Gli strumenti dell'interfaccia della riga di comando di .NET sono inclusi in .NET SDK a partire dalla versione 2.1, in modo che il riferimento a tale pacchetto possa essere rimosso dal file del progetto:</span><span class="sxs-lookup"><span data-stu-id="4ac60-170">The .NET CLI tools are included in the .NET SDK since version 2.1, so the reference to that package can be removed from the project file:</span></span>

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
