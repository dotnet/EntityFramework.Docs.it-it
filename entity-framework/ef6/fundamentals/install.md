---
title: Ottenere Entity Framework - Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
caps.latest.revision: 4
ms.openlocfilehash: 400bf1428e6754a88dbc1264c346bb66282725a0
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122751"
---
# <a name="get-entity-framework"></a><span data-ttu-id="b9bef-102">Ottenere Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b9bef-102">Get Entity Framework</span></span>
<span data-ttu-id="b9bef-103">Entity Framework è costituito da strumenti di Entity Framework per Visual Studio e il Runtime di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b9bef-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="b9bef-104">Entity Framework Tools per Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9bef-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="b9bef-105">Entity Framework Tools per Visual Studio includono Entity Framework Designer e la creazione guidata modello di Entity Framework e sono necessari per il database prima di tutto e modellare i flussi di lavoro prima.</span><span class="sxs-lookup"><span data-stu-id="b9bef-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="b9bef-106">Gli strumenti di Entity Framework sono inclusi in tutte le versioni recenti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b9bef-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="b9bef-107">Se si esegue un'installazione personalizzata di Visual Studio è necessario assicurarsi che l'elemento "Strumenti di Entity Framework 6" è selezionato uno scegliendo un carico di lavoro di cui è incluso o selezionandolo come un singolo componente.</span><span class="sxs-lookup"><span data-stu-id="b9bef-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="b9bef-108">Per alcune versioni precedenti di Visual Studio, sono disponibili come download aggiornato gli strumenti di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b9bef-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="b9bef-109">Visualizzare [versioni di Visual Studio](~/ef6/what-is-new/visual-studio.md) per indicazioni su come ottenere la versione più recente degli strumenti di Entity Framework disponibili per la versione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b9bef-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="b9bef-110">Runtime di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b9bef-110">EF Runtime</span></span>

<span data-ttu-id="b9bef-111">La versione più recente di Entity Framework è disponibile come le [pacchetto EntityFramework NuGet](http://nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="b9bef-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](http://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="b9bef-112">Se non si ha familiarità con Gestione pacchetti NuGet, si consiglia di leggere il [Panoramica di NuGet](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span><span class="sxs-lookup"><span data-stu-id="b9bef-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="b9bef-113">Installazione del pacchetto NuGet di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b9bef-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="b9bef-114">È possibile installare il pacchetto di EntityFramework facendo clic sui **riferimenti** cartella del progetto e selezionando **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="b9bef-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![ManageNuGetPackages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="b9bef-116">Installazione dalla Console di gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="b9bef-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="b9bef-117">In alternativa, è possibile installare EntityFramework eseguendo il comando seguente nel [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="b9bef-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="b9bef-118">Installare una versione specifica di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b9bef-118">Installing a specific version of EF</span></span>

<span data-ttu-id="b9bef-119">Da Entity Framework 4.1 e versioni successive, sono state rilasciate nuove versioni del runtime di Entity Framework come la [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="b9bef-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Pacakge](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="b9bef-120">Qualsiasi di queste versioni possono essere aggiunti a un progetto basato su .NET Framework eseguendo il comando seguente in Visual Studio [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span><span class="sxs-lookup"><span data-stu-id="b9bef-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="b9bef-121">Si noti che `<number>` rappresenta la specifica versione di Entity Framework per l'installazione.</span><span class="sxs-lookup"><span data-stu-id="b9bef-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="b9bef-122">Ad esempio, 6.2.0 è la versione del numero di EF 6.2.</span><span class="sxs-lookup"><span data-stu-id="b9bef-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="b9bef-123">Runtime di Entity Framework prima 4.1 facevano parte di .NET Framework e non può essere installato separatamente.</span><span class="sxs-lookup"><span data-stu-id="b9bef-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="b9bef-124">Installare l'anteprima più recente</span><span class="sxs-lookup"><span data-stu-id="b9bef-124">Installing the Latest Preview</span></span>

<span data-ttu-id="b9bef-125">I metodi sopra indicati fornirà la versione più recente versione di Entity Framework è supportata completamente.</span><span class="sxs-lookup"><span data-stu-id="b9bef-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="b9bef-126">Spesso esistono versioni non definitive di Entity Framework che mi piacerebbe poter provare e inviare commenti e suggerimenti su disponibili.</span><span class="sxs-lookup"><span data-stu-id="b9bef-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="b9bef-127">Per installare l'anteprima più recente di Entity Framework è possibile selezionare **Includi versione provvisoria** nella finestra Gestisci pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="b9bef-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="b9bef-128">Se non le versioni non definitive sono disponibili si otterrà automaticamente la versione più recente versione completamente supportata di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b9bef-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![IncludePreRelease](~/ef6/media/includeprerelease.png)

<span data-ttu-id="b9bef-130">In alternativa, è possibile eseguire il comando seguente [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="b9bef-130">Alternatively, you can run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```