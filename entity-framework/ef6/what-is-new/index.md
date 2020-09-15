---
title: Novità - EF6
description: Novità di Entity Framework 6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 71854e0a7009b35742a70724210708a284f50a3d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619763"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="b0763-103">Novità di EF6</span><span class="sxs-lookup"><span data-stu-id="b0763-103">What's new in EF6</span></span>

<span data-ttu-id="b0763-104">Per ottenere le funzionalità più recenti e il livello di stabilità più elevato è consigliabile usare l'ultima versione rilasciata di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b0763-104">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="b0763-105">È possibile, tuttavia, usare una versione precedente o provare i miglioramenti resi disponibili nella versione provvisoria più recente.</span><span class="sxs-lookup"><span data-stu-id="b0763-105">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="b0763-106">Per installare versioni specifiche di Entity Framework, vedere [Get Entity Framework](xref:ef6/fundamentals/install) (Ottenere Entity Framework).</span><span class="sxs-lookup"><span data-stu-id="b0763-106">To install specific versions of EF, see [Get Entity Framework](xref:ef6/fundamentals/install).</span></span>

## <a name="ef-640"></a><span data-ttu-id="b0763-107">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="b0763-107">EF 6.4.0</span></span>

<span data-ttu-id="b0763-108">Il runtime di EF 6.4.0 è stato rilasciato in NuGet nel mese di dicembre 2019.</span><span class="sxs-lookup"><span data-stu-id="b0763-108">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="b0763-109">L'obiettivo principale di EF 6.4 è quello di rifinire le funzionalità e gli scenari forniti in EF 6.3.</span><span class="sxs-lookup"><span data-stu-id="b0763-109">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="b0763-110">Vedere l'[elenco delle correzioni importanti](https://github.com/dotnet/ef6/milestone/14?closed=1) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="b0763-110">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="b0763-111">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="b0763-111">EF 6.3.0</span></span>

<span data-ttu-id="b0763-112">Il runtime di EF 6.3.0 è stato rilasciato in NuGet nel mese di settembre 2019.</span><span class="sxs-lookup"><span data-stu-id="b0763-112">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="b0763-113">L'obiettivo principale di questa versione è quello di semplificare la migrazione delle applicazioni esistenti che usano EF 6 a .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b0763-113">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="b0763-114">La community ha anche contribuito a diverse correzioni di bug e miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="b0763-114">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="b0763-115">Per informazioni dettagliate, vedere i problemi chiusi in ogni [fase cardine](https://github.com/aspnet/EntityFramework6/milestones?state=closed) della versione 6.3.0.</span><span class="sxs-lookup"><span data-stu-id="b0763-115">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="b0763-116">Ecco alcuni dei più importanti:</span><span class="sxs-lookup"><span data-stu-id="b0763-116">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="b0763-117">Supporto per .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="b0763-117">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="b0763-118">Il pacchetto EntityFramework ora ha come destinazione .NET Standard 2.1 oltre a .NET Framework 4.x.</span><span class="sxs-lookup"><span data-stu-id="b0763-118">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="b0763-119">Questo significa che EF 6.3 è multipiattaforma e supportato in altri sistemi operativi oltre a Windows, come Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="b0763-119">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="b0763-120">I comandi per le migrazioni sono stati riscritti per l'esecuzione out-of-process e supportano i progetti in stile SDK.</span><span class="sxs-lookup"><span data-stu-id="b0763-120">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="b0763-121">Supporto di HierarchyId di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b0763-121">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="b0763-122">Compatibilità migliorata con Roslyn e NuGet PackageReference.</span><span class="sxs-lookup"><span data-stu-id="b0763-122">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="b0763-123">Aggiunta dell'utilità `ef6.exe` per l'abilitazione, l'aggiunta, l'esecuzione di script e l'applicazione di migrazioni da assembly.</span><span class="sxs-lookup"><span data-stu-id="b0763-123">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="b0763-124">Sostituisce `migrate.exe`.</span><span class="sxs-lookup"><span data-stu-id="b0763-124">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="b0763-125">Supporto di EF Designer</span><span class="sxs-lookup"><span data-stu-id="b0763-125">EF designer support</span></span>

<span data-ttu-id="b0763-126">Attualmente non è disponibile alcun supporto per l'uso di EF Designer direttamente nei progetti .NET Core o .NET Standard oppure nei progetti .NET Framework in stile SDK.</span><span class="sxs-lookup"><span data-stu-id="b0763-126">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="b0763-127">Per ovviare a questa limitazione, è possibile aggiungere il file EDMX e le classi generate per le entità e DbContext come file collegati a un progetto .NET Core 3.0 o .NET Standard 2.1 nella stessa soluzione.</span><span class="sxs-lookup"><span data-stu-id="b0763-127">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="b0763-128">Nel file di progetto i file collegati appariranno come segue:</span><span class="sxs-lookup"><span data-stu-id="b0763-128">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="b0763-129">Si noti che il file EDMX è collegato all'azione di compilazione EntityDeploy.</span><span class="sxs-lookup"><span data-stu-id="b0763-129">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="b0763-130">Si tratta di un'attività MSBuild speciale (ora inclusa nel pacchetto EF 6.3) che gestisce l'aggiunta del modello EF nell'assembly di destinazione come risorse incorporate (o la copia come file nella cartella di output, a seconda dell'impostazione di elaborazione degli artefatti dei metadati in EDMX).</span><span class="sxs-lookup"><span data-stu-id="b0763-130">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="b0763-131">Per altri dettagli su come ottenere questa configurazione, vedere l'[esempio EDMX .NET Core](https://aka.ms/EdmxDotNetCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b0763-131">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="b0763-132">Avviso: assicurarsi che il progetto .NET Framework vecchio stile (ovvero non in stile SDK), che definisce il file con estensione edmx "reale" venga _prima_ del progetto che definisce il collegamento all'interno del file con estensione sln.</span><span class="sxs-lookup"><span data-stu-id="b0763-132">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="b0763-133">In caso contrario, quando si apre il file con estensione edmx nella finestra di progettazione, viene visualizzato il messaggio di errore "Entity Framework non è disponibile nel framework di destinazione attualmente specificato per il progetto.</span><span class="sxs-lookup"><span data-stu-id="b0763-133">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="b0763-134">È possibile modificare il framework di destinazione del progetto oppure modificare il modello in XmlEditor".</span><span class="sxs-lookup"><span data-stu-id="b0763-134">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="b0763-135">Versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="b0763-135">Past Releases</span></span>

<span data-ttu-id="b0763-136">La pagina [Versioni precedenti](xref:ef6/what-is-new/past-releases) contiene un archivio di tutte le versioni precedenti di Entity Framework e delle funzionalità principali introdotte in ogni versione.</span><span class="sxs-lookup"><span data-stu-id="b0763-136">The [Past Releases](xref:ef6/what-is-new/past-releases) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
