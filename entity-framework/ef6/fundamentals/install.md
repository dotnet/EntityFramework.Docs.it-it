---
title: Ottenere Entity Framework-EF6
description: Ottenere Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616252"
---
# <a name="get-entity-framework"></a><span data-ttu-id="4a04d-103">Ottenere Entity Framework</span><span class="sxs-lookup"><span data-stu-id="4a04d-103">Get Entity Framework</span></span>
<span data-ttu-id="4a04d-104">Entity Framework è costituito da EF Tools per Visual Studio e dal runtime di EF.</span><span class="sxs-lookup"><span data-stu-id="4a04d-104">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="4a04d-105">EF Tools per Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a04d-105">EF Tools for Visual Studio</span></span>

<span data-ttu-id="4a04d-106">I Entity Framework Tools per Visual Studio includono EF designer e la procedura guidata modello EF e sono necessari per i flussi di lavoro First e Model First del database.</span><span class="sxs-lookup"><span data-stu-id="4a04d-106">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="4a04d-107">Gli strumenti EF sono inclusi in tutte le versioni recenti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a04d-107">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="4a04d-108">Se si esegue un'installazione personalizzata di Visual Studio, è necessario assicurarsi che sia selezionato l'elemento "Entity Framework 6 Tools" scegliendo un carico di lavoro che lo includa o selezionandolo come singolo componente.</span><span class="sxs-lookup"><span data-stu-id="4a04d-108">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="4a04d-109">Per alcune versioni precedenti di Visual Studio, gli strumenti EF aggiornati sono disponibili come download.</span><span class="sxs-lookup"><span data-stu-id="4a04d-109">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="4a04d-110">Vedere [versioni di Visual Studio](xref:ef6/what-is-new/visual-studio) per istruzioni su come ottenere la versione più recente degli strumenti EF per la versione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a04d-110">See [Visual Studio Versions](xref:ef6/what-is-new/visual-studio) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="4a04d-111">Runtime EF</span><span class="sxs-lookup"><span data-stu-id="4a04d-111">EF Runtime</span></span>

<span data-ttu-id="4a04d-112">La versione più recente di Entity Framework è disponibile come [pacchetto NuGet EntityFramework](https://nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="4a04d-112">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="4a04d-113">Se non si ha familiarità con gestione pacchetti NuGet, si consiglia di leggere la [Panoramica di NuGet](/nuget/consume-packages/overview-and-workflow).</span><span class="sxs-lookup"><span data-stu-id="4a04d-113">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="4a04d-114">Installazione del pacchetto NuGet EF</span><span class="sxs-lookup"><span data-stu-id="4a04d-114">Installing the EF NuGet Package</span></span>

<span data-ttu-id="4a04d-115">È possibile installare il pacchetto EntityFramework facendo clic con il pulsante destro del mouse sulla cartella **riferimenti** del progetto e scegliendo **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="4a04d-115">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![Manage NuGet Packages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="4a04d-117">Installazione dalla console di gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="4a04d-117">Installing from Package Manager Console</span></span>

<span data-ttu-id="4a04d-118">In alternativa, è possibile installare EntityFramework eseguendo il comando seguente nella console di [Gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="4a04d-118">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="4a04d-119">Installazione di una versione specifica di EF</span><span class="sxs-lookup"><span data-stu-id="4a04d-119">Installing a specific version of EF</span></span>

<span data-ttu-id="4a04d-120">Da EF 4,1 in poi, le nuove versioni di EF Runtime sono state rilasciate come [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="4a04d-120">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="4a04d-121">È possibile aggiungere una qualsiasi di queste versioni a un progetto basato su .NET Framework eseguendo il comando seguente nella [console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4a04d-121">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="4a04d-122">Si noti che `<number>` rappresenta la versione specifica di EF da installare.</span><span class="sxs-lookup"><span data-stu-id="4a04d-122">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="4a04d-123">Ad esempio, 6.2.0 è la versione di numero per EF 6,2.</span><span class="sxs-lookup"><span data-stu-id="4a04d-123">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="4a04d-124">I runtime EF prima del 4,1 facevano parte di .NET Framework e non possono essere installati separatamente.</span><span class="sxs-lookup"><span data-stu-id="4a04d-124">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="4a04d-125">Installazione dell'anteprima più recente</span><span class="sxs-lookup"><span data-stu-id="4a04d-125">Installing the Latest Preview</span></span>

<span data-ttu-id="4a04d-126">I metodi precedenti forniranno la versione più recente di Entity Framework supportata.</span><span class="sxs-lookup"><span data-stu-id="4a04d-126">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="4a04d-127">Spesso sono disponibili versioni non definitive di Entity Framework che ci piacerebbero provare e inviare commenti e suggerimenti su.</span><span class="sxs-lookup"><span data-stu-id="4a04d-127">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="4a04d-128">Per installare l'anteprima più recente di EntityFramework, è possibile selezionare **Includi versione preliminare** nella finestra Gestisci pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="4a04d-128">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="4a04d-129">Se non sono disponibili versioni non definitive, verrà automaticamente scaricata la versione di Entity Framework più recente supportata.</span><span class="sxs-lookup"><span data-stu-id="4a04d-129">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![Includi versione preliminare](~/ef6/media/includeprerelease.png)

<span data-ttu-id="4a04d-131">In alternativa, è possibile eseguire il comando seguente nella [console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="4a04d-131">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
