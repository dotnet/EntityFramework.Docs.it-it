---
title: Guida di riferimento agli strumenti di EF Core (console di gestione pacchetti)-EF Core
description: Guida di riferimento per la console di gestione pacchetti Entity Framework Core Visual Studio
author: bricelam
ms.author: bricelam
ms.date: 09/09/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 5dca397978c60c12610d9080caba972a66b079b6
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071862"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="f7168-103">Guida di riferimento agli strumenti di Entity Framework Core-Console di gestione pacchetti in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7168-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="f7168-104">Gli strumenti della console di gestione pacchetti (PMC) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="f7168-105">Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations), applicano migrazioni e generano codice per un modello basato su un database esistente.</span><span class="sxs-lookup"><span data-stu-id="f7168-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="f7168-106">I comandi vengono eseguiti all'interno di Visual Studio tramite la [console di gestione pacchetti](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="f7168-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="f7168-107">Questi strumenti funzionano sia con i progetti .NET Framework che con i progetti .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7168-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="f7168-108">Se non si usa Visual Studio, è consigliabile usare gli [strumenti da riga di comando EF Core](xref:core/miscellaneous/cli/dotnet) .</span><span class="sxs-lookup"><span data-stu-id="f7168-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/miscellaneous/cli/dotnet) instead.</span></span> <span data-ttu-id="f7168-109">Gli strumenti interfaccia della riga di comando di .NET Core sono multipiattaforma ed eseguiti all'interno di un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="f7168-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="f7168-110">Installazione degli strumenti</span><span class="sxs-lookup"><span data-stu-id="f7168-110">Installing the tools</span></span>

<span data-ttu-id="f7168-111">Le procedure per l'installazione e l'aggiornamento degli strumenti sono diverse tra ASP.NET Core 2.1 + e le versioni precedenti o altri tipi di progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-111">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="f7168-112">ASP.NET Core versione 2,1 e successive</span><span class="sxs-lookup"><span data-stu-id="f7168-112">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="f7168-113">Gli strumenti vengono inclusi automaticamente in un progetto ASP.NET Core 2.1 + perché il `Microsoft.EntityFrameworkCore.Tools` pacchetto è incluso nel [metapacchetto Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f7168-113">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f7168-114">Pertanto, non è necessario eseguire alcuna operazione per installare gli strumenti, ma è necessario:</span><span class="sxs-lookup"><span data-stu-id="f7168-114">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="f7168-115">Ripristinare i pacchetti prima di utilizzare gli strumenti in un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-115">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="f7168-116">Installare un pacchetto per aggiornare gli strumenti a una versione più recente.</span><span class="sxs-lookup"><span data-stu-id="f7168-116">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="f7168-117">Per assicurarsi che si stia ricevendo la versione più recente degli strumenti, è consigliabile eseguire anche il passaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="f7168-117">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="f7168-118">Modificare il file con *estensione csproj* e aggiungere una riga che specifichi la versione più recente del pacchetto [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="f7168-118">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="f7168-119">Ad esempio, il file con *estensione csproj* potrebbe includere un oggetto `ItemGroup` simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="f7168-119">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="f7168-120">Aggiornare gli strumenti quando si riceve un messaggio simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f7168-120">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="f7168-121">La versione di EF Core Tools ' 2.1.1-RTM-30846' è antecedente a quella del runtime ' 2.1.3-RTM-32065'.</span><span class="sxs-lookup"><span data-stu-id="f7168-121">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="f7168-122">Aggiornare gli strumenti per le funzionalità e le correzioni di bug più recenti.</span><span class="sxs-lookup"><span data-stu-id="f7168-122">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="f7168-123">Per aggiornare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="f7168-123">To update the tools:</span></span>

* <span data-ttu-id="f7168-124">installare la versione più recente di .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f7168-124">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="f7168-125">Aggiornare Visual Studio alla versione più recente.</span><span class="sxs-lookup"><span data-stu-id="f7168-125">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="f7168-126">Modificare il file con *estensione csproj* in modo da includere un riferimento al pacchetto degli strumenti più recenti, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-126">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="f7168-127">Altre versioni e tipi di progetto</span><span class="sxs-lookup"><span data-stu-id="f7168-127">Other versions and project types</span></span>

<span data-ttu-id="f7168-128">Installare gli strumenti della console di gestione pacchetti eseguendo il comando seguente nella **console di gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="f7168-128">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f7168-129">Aggiornare gli strumenti eseguendo il comando seguente nella **console di gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="f7168-129">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="f7168-130">Verificare l'installazione</span><span class="sxs-lookup"><span data-stu-id="f7168-130">Verify the installation</span></span>

<span data-ttu-id="f7168-131">Verificare che gli strumenti siano installati eseguendo questo comando:</span><span class="sxs-lookup"><span data-stu-id="f7168-131">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="f7168-132">L'output ha un aspetto simile al seguente (non indica la versione degli strumenti in uso):</span><span class="sxs-lookup"><span data-stu-id="f7168-132">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="f7168-133">Uso degli strumenti</span><span class="sxs-lookup"><span data-stu-id="f7168-133">Using the tools</span></span>

<span data-ttu-id="f7168-134">Prima di usare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="f7168-134">Before using the tools:</span></span>

* <span data-ttu-id="f7168-135">Comprendere la differenza tra il progetto di destinazione e quello di avvio.</span><span class="sxs-lookup"><span data-stu-id="f7168-135">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="f7168-136">Informazioni su come usare gli strumenti con le librerie di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="f7168-136">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="f7168-137">Per ASP.NET Core progetti, impostare l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="f7168-137">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="f7168-138">Progetto di destinazione e di avvio</span><span class="sxs-lookup"><span data-stu-id="f7168-138">Target and startup project</span></span>

<span data-ttu-id="f7168-139">I comandi fanno riferimento a un *progetto e a* un *progetto di avvio*.</span><span class="sxs-lookup"><span data-stu-id="f7168-139">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="f7168-140">Il *progetto* è anche noto come *progetto di destinazione* perché è il punto in cui i comandi aggiungono o rimuovono i file.</span><span class="sxs-lookup"><span data-stu-id="f7168-140">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="f7168-141">Per impostazione predefinita, il progetto **predefinito** selezionato nella **console di gestione pacchetti** è il progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-141">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="f7168-142">È possibile specificare un progetto diverso come progetto di destinazione utilizzando l' <nobr>`--project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="f7168-142">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="f7168-143">Il *progetto di avvio* è quello che gli strumenti compilano ed eseguono.</span><span class="sxs-lookup"><span data-stu-id="f7168-143">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="f7168-144">Gli strumenti di devono eseguire il codice dell'applicazione in fase di progettazione per ottenere informazioni sul progetto, ad esempio la stringa di connessione al database e la configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="f7168-144">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="f7168-145">Per impostazione predefinita, il **progetto di avvio** in **Esplora soluzioni** è il progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="f7168-145">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="f7168-146">È possibile specificare un progetto diverso come progetto di avvio usando l' <nobr>`--startup-project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="f7168-146">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="f7168-147">Il progetto di avvio e il progetto di destinazione sono spesso lo stesso progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-147">The startup project and target project are often the same project.</span></span> <span data-ttu-id="f7168-148">Uno scenario tipico in cui si trovano progetti distinti è quando:</span><span class="sxs-lookup"><span data-stu-id="f7168-148">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="f7168-149">Il contesto EF Core e le classi di entità si trovano in una libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7168-149">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="f7168-150">Un'app console o Web .NET Core fa riferimento alla libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="f7168-150">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="f7168-151">È anche possibile [inserire il codice delle migrazioni in una libreria di classi separata dal contesto EF Core](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="f7168-151">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="f7168-152">Altri Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="f7168-152">Other target frameworks</span></span>

<span data-ttu-id="f7168-153">Gli strumenti della console di gestione pacchetti funzionano con i progetti .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f7168-153">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="f7168-154">Le app che hanno il modello di EF Core in una libreria di classi .NET Standard potrebbero non avere un progetto .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f7168-154">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="f7168-155">Ad esempio, questo vale per le app Novell e piattaforma UWP (Universal Windows Platform).</span><span class="sxs-lookup"><span data-stu-id="f7168-155">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="f7168-156">In questi casi, è possibile creare un progetto di app console .NET Core o .NET Framework il cui unico scopo è fungere da progetto di avvio per gli strumenti di.</span><span class="sxs-lookup"><span data-stu-id="f7168-156">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="f7168-157">Il progetto può essere un progetto fittizio senza codice reale &mdash; necessario per fornire una destinazione per gli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f7168-157">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="f7168-158">Perché è necessario un progetto fittizio?</span><span class="sxs-lookup"><span data-stu-id="f7168-158">Why is a dummy project required?</span></span> <span data-ttu-id="f7168-159">Come indicato in precedenza, gli strumenti devono eseguire il codice dell'applicazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-159">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="f7168-160">A tale scopo, è necessario usare .NET Core o .NET Framework Runtime.</span><span class="sxs-lookup"><span data-stu-id="f7168-160">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="f7168-161">Quando il modello di EF Core si trova in un progetto destinato a .NET Core o .NET Framework, gli strumenti di EF Core prendono in prestito il runtime dal progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-161">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="f7168-162">Questa operazione non può essere eseguita se il modello di EF Core si trova in una libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="f7168-162">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="f7168-163">Il .NET Standard non è un'implementazione .NET effettiva; si tratta di una specifica di un set di API che le implementazioni di .NET devono supportare.</span><span class="sxs-lookup"><span data-stu-id="f7168-163">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="f7168-164">Pertanto .NET Standard non è sufficiente per gli strumenti EF Core per eseguire il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-164">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="f7168-165">Il progetto fittizio creato da usare come progetto di avvio fornisce una piattaforma di destinazione concreta in cui gli strumenti possono caricare la libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="f7168-165">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="f7168-166">Ambiente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7168-166">ASP.NET Core environment</span></span>

<span data-ttu-id="f7168-167">Per specificare l'ambiente per i progetti ASP.NET Core, impostare **env: ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.</span><span class="sxs-lookup"><span data-stu-id="f7168-167">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="f7168-168">Parametri comuni</span><span class="sxs-lookup"><span data-stu-id="f7168-168">Common parameters</span></span>

<span data-ttu-id="f7168-169">La tabella seguente illustra i parametri comuni a tutti i comandi EF Core:</span><span class="sxs-lookup"><span data-stu-id="f7168-169">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="f7168-170">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-170">Parameter</span></span>                 | <span data-ttu-id="f7168-171">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-171">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-172">-Contesto \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-172">-Context \<String></span></span>        | <span data-ttu-id="f7168-173">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="f7168-173">The `DbContext` class to use.</span></span> <span data-ttu-id="f7168-174">Solo il nome della classe o completo con gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="f7168-174">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="f7168-175">Se questo parametro viene omesso, EF Core trova la classe del contesto.</span><span class="sxs-lookup"><span data-stu-id="f7168-175">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="f7168-176">Se sono presenti più classi di contesto, questo parametro è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f7168-176">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="f7168-177">-Progetto \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-177">-Project \<String></span></span>        | <span data-ttu-id="f7168-178">Progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-178">The target project.</span></span> <span data-ttu-id="f7168-179">Se questo parametro viene omesso, come progetto di destinazione viene utilizzato il **progetto predefinito** per la **console di gestione pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="f7168-179">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="f7168-180"><nobr>-Progetto</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="f7168-180"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="f7168-181">Progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="f7168-181">The startup project.</span></span> <span data-ttu-id="f7168-182">Se questo parametro viene omesso, il **progetto di avvio** nelle **proprietà della soluzione** viene utilizzato come progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-182">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="f7168-183">-Argomenti \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-183">-Args \<String></span></span>           | <span data-ttu-id="f7168-184">Argomenti passati all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-184">Arguments passed to the application.</span></span> <span data-ttu-id="f7168-185">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-185">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="f7168-186">-Verbose</span><span class="sxs-lookup"><span data-stu-id="f7168-186">-Verbose</span></span>                  | <span data-ttu-id="f7168-187">Mostra output dettagliato.</span><span class="sxs-lookup"><span data-stu-id="f7168-187">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="f7168-188">Per visualizzare le informazioni della guida relative a un comando, usare il comando di PowerShell `Get-Help` .</span><span class="sxs-lookup"><span data-stu-id="f7168-188">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="f7168-189">Il contesto, il progetto e i parametri progetto supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="f7168-189">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="f7168-190">Aggiungi-migrazione</span><span class="sxs-lookup"><span data-stu-id="f7168-190">Add-Migration</span></span>

<span data-ttu-id="f7168-191">Aggiunge una nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-191">Adds a new migration.</span></span>

<span data-ttu-id="f7168-192">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-192">Parameters:</span></span>

| <span data-ttu-id="f7168-193">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-193">Parameter</span></span>                         | <span data-ttu-id="f7168-194">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-194">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-195"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-195"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="f7168-196">Nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-196">The name of the migration.</span></span> <span data-ttu-id="f7168-197">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f7168-197">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="f7168-198"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-198"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="f7168-199">Directory usata per l'output dei file.</span><span class="sxs-lookup"><span data-stu-id="f7168-199">The directory use to output the files.</span></span> <span data-ttu-id="f7168-200">I percorsi sono relativi alla directory del progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-200">Paths are relative to the target project directory.</span></span> <span data-ttu-id="f7168-201">Il valore predefinito è "Migrations".</span><span class="sxs-lookup"><span data-stu-id="f7168-201">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="f7168-202"><nobr>-Spazio dei nomi \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-202"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="f7168-203">Spazio dei nomi da utilizzare per le classi generate.</span><span class="sxs-lookup"><span data-stu-id="f7168-203">The namespace to use for the generated classes.</span></span> <span data-ttu-id="f7168-204">Il valore predefinito è generato dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="f7168-204">Defaults to generated from the output directory.</span></span> <span data-ttu-id="f7168-205">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-205">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="f7168-206">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-206">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="f7168-207">Drop-database</span><span class="sxs-lookup"><span data-stu-id="f7168-207">Drop-Database</span></span>

<span data-ttu-id="f7168-208">Elimina il database.</span><span class="sxs-lookup"><span data-stu-id="f7168-208">Drops the database.</span></span>

<span data-ttu-id="f7168-209">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-209">Parameters:</span></span>

| <span data-ttu-id="f7168-210">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-210">Parameter</span></span> | <span data-ttu-id="f7168-211">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-211">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="f7168-212">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="f7168-212">-WhatIf</span></span>   | <span data-ttu-id="f7168-213">Indica il database da eliminare, ma non eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="f7168-213">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="f7168-214">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-214">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="f7168-215">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="f7168-215">Get-DbContext</span></span>

<span data-ttu-id="f7168-216">Elenca e ottiene informazioni sui `DbContext` tipi disponibili.</span><span class="sxs-lookup"><span data-stu-id="f7168-216">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="f7168-217">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-217">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="f7168-218">Get-Migration</span><span class="sxs-lookup"><span data-stu-id="f7168-218">Get-Migration</span></span>

<span data-ttu-id="f7168-219">Elenca le migrazioni disponibili.</span><span class="sxs-lookup"><span data-stu-id="f7168-219">Lists available migrations.</span></span> <span data-ttu-id="f7168-220">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-220">Added in EF Core 5.0.</span></span>

<span data-ttu-id="f7168-221">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-221">Parameters:</span></span>

| <span data-ttu-id="f7168-222">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-222">Parameter</span></span>                          | <span data-ttu-id="f7168-223">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-223">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="f7168-224"><nobr>-Connessione \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-224"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="f7168-225">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="f7168-225">The connection string to the database.</span></span> <span data-ttu-id="f7168-226">Il valore predefinito è quello specificato in AddDbContext o Configuring.</span><span class="sxs-lookup"><span data-stu-id="f7168-226">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="f7168-227">-Noconnect</span><span class="sxs-lookup"><span data-stu-id="f7168-227">-NoConnect</span></span>                         | <span data-ttu-id="f7168-228">Non connettersi al database.</span><span class="sxs-lookup"><span data-stu-id="f7168-228">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="f7168-229">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-229">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="f7168-230">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="f7168-230">Remove-Migration</span></span>

<span data-ttu-id="f7168-231">Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).</span><span class="sxs-lookup"><span data-stu-id="f7168-231">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="f7168-232">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-232">Parameters:</span></span>

| <span data-ttu-id="f7168-233">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-233">Parameter</span></span> | <span data-ttu-id="f7168-234">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-234">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-235">-Force</span><span class="sxs-lookup"><span data-stu-id="f7168-235">-Force</span></span>    | <span data-ttu-id="f7168-236">Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database.</span><span class="sxs-lookup"><span data-stu-id="f7168-236">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="f7168-237">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-237">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="f7168-238">Impalcature-DbContext</span><span class="sxs-lookup"><span data-stu-id="f7168-238">Scaffold-DbContext</span></span>

<span data-ttu-id="f7168-239">Genera il codice per un `DbContext` tipo di entità e per un database.</span><span class="sxs-lookup"><span data-stu-id="f7168-239">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="f7168-240">Per `Scaffold-DbContext` poter generare un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="f7168-240">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="f7168-241">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-241">Parameters:</span></span>

| <span data-ttu-id="f7168-242">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-242">Parameter</span></span>                          | <span data-ttu-id="f7168-243">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-243">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-244"><nobr>-Connessione \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-244"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="f7168-245">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="f7168-245">The connection string to the database.</span></span> <span data-ttu-id="f7168-246">Per i progetti ASP.NET Core 2. x, il valore può essere *Name \<name of connection string> =*.</span><span class="sxs-lookup"><span data-stu-id="f7168-246">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="f7168-247">In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-247">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="f7168-248">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f7168-248">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="f7168-249"><nobr>-Provider \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-249"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="f7168-250">Provider da usare.</span><span class="sxs-lookup"><span data-stu-id="f7168-250">The provider to use.</span></span> <span data-ttu-id="f7168-251">Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="f7168-251">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="f7168-252">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f7168-252">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="f7168-253">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-253">-OutputDir \<String></span></span>               | <span data-ttu-id="f7168-254">Directory in cui inserire i file.</span><span class="sxs-lookup"><span data-stu-id="f7168-254">The directory to put files in.</span></span> <span data-ttu-id="f7168-255">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-255">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="f7168-256">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-256">-ContextDir \<String></span></span>              | <span data-ttu-id="f7168-257">Directory in cui inserire il `DbContext` file.</span><span class="sxs-lookup"><span data-stu-id="f7168-257">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="f7168-258">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="f7168-258">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="f7168-259">-Spazio dei nomi \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-259">-Namespace \<String></span></span>               | <span data-ttu-id="f7168-260">Spazio dei nomi da utilizzare per tutte le classi generate.</span><span class="sxs-lookup"><span data-stu-id="f7168-260">The namespace to use for all generated classes.</span></span> <span data-ttu-id="f7168-261">Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="f7168-261">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="f7168-262">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-262">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="f7168-263">-ContextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-263">-ContextNamespace \<String></span></span>        | <span data-ttu-id="f7168-264">Spazio dei nomi da utilizzare per la `DbContext` classe generata.</span><span class="sxs-lookup"><span data-stu-id="f7168-264">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="f7168-265">Nota: esegue l'override di `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="f7168-265">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="f7168-266">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-266">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="f7168-267">-Contesto \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-267">-Context \<String></span></span>                 | <span data-ttu-id="f7168-268">Nome della `DbContext` classe da generare.</span><span class="sxs-lookup"><span data-stu-id="f7168-268">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="f7168-269">-Schemi \<String[]></span><span class="sxs-lookup"><span data-stu-id="f7168-269">-Schemas \<String[]></span></span>               | <span data-ttu-id="f7168-270">Schemi delle tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="f7168-270">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="f7168-271">Se questo parametro viene omesso, vengono inclusi tutti gli schemi.</span><span class="sxs-lookup"><span data-stu-id="f7168-271">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="f7168-272">-Tabelle \<String[]></span><span class="sxs-lookup"><span data-stu-id="f7168-272">-Tables \<String[]></span></span>                | <span data-ttu-id="f7168-273">Tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="f7168-273">The tables to generate entity types for.</span></span> <span data-ttu-id="f7168-274">Se questo parametro viene omesso, vengono incluse tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="f7168-274">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="f7168-275">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="f7168-275">-DataAnnotations</span></span>                   | <span data-ttu-id="f7168-276">Utilizzare gli attributi per configurare il modello (laddove possibile).</span><span class="sxs-lookup"><span data-stu-id="f7168-276">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f7168-277">Se questo parametro viene omesso, viene usata solo l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="f7168-277">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="f7168-278">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="f7168-278">-UseDatabaseNames</span></span>                  | <span data-ttu-id="f7168-279">Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database.</span><span class="sxs-lookup"><span data-stu-id="f7168-279">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="f7168-280">Se questo parametro viene omesso, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#.</span><span class="sxs-lookup"><span data-stu-id="f7168-280">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="f7168-281">-Force</span><span class="sxs-lookup"><span data-stu-id="f7168-281">-Force</span></span>                             | <span data-ttu-id="f7168-282">Sovrascrivere i file esistenti.</span><span class="sxs-lookup"><span data-stu-id="f7168-282">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="f7168-283">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="f7168-283">-NoOnConfiguring</span></span>                   | <span data-ttu-id="f7168-284">Non generare `DbContext.OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="f7168-284">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="f7168-285">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-285">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="f7168-286">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="f7168-286">-NoPluralize</span></span>                       | <span data-ttu-id="f7168-287">Non usare pluralizer.</span><span class="sxs-lookup"><span data-stu-id="f7168-287">Don't use the pluralizer.</span></span> <span data-ttu-id="f7168-288">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-288">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="f7168-289">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-289">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="f7168-290">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f7168-290">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="f7168-291">Esempio che associa solo le tabelle selezionate e crea il contesto in una cartella separata con il nome e lo spazio dei nomi specificati:</span><span class="sxs-lookup"><span data-stu-id="f7168-291">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a><span data-ttu-id="f7168-292">Script-DbContext</span><span class="sxs-lookup"><span data-stu-id="f7168-292">Script-DbContext</span></span>

<span data-ttu-id="f7168-293">Genera uno script SQL da DbContext.</span><span class="sxs-lookup"><span data-stu-id="f7168-293">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="f7168-294">Ignora le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="f7168-294">Bypasses any migrations.</span></span> <span data-ttu-id="f7168-295">Aggiunto in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-295">Added in EF Core 3.0.</span></span>

<span data-ttu-id="f7168-296">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-296">Parameters:</span></span>

| <span data-ttu-id="f7168-297">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-297">Parameter</span></span>                      | <span data-ttu-id="f7168-298">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-298">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="f7168-299"><nobr>-Output \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-299"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="f7168-300">File in cui scrivere il risultato.</span><span class="sxs-lookup"><span data-stu-id="f7168-300">The file to write the result to.</span></span> |

<span data-ttu-id="f7168-301">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-301">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="f7168-302">Script-migrazione</span><span class="sxs-lookup"><span data-stu-id="f7168-302">Script-Migration</span></span>

<span data-ttu-id="f7168-303">Genera uno script SQL che applica tutte le modifiche a una migrazione selezionata a un'altra.</span><span class="sxs-lookup"><span data-stu-id="f7168-303">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="f7168-304">Parametri</span><span class="sxs-lookup"><span data-stu-id="f7168-304">Parameters:</span></span>

| <span data-ttu-id="f7168-305">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-305">Parameter</span></span>                    | <span data-ttu-id="f7168-306">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-306">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-307">*-Da*\<String></span><span class="sxs-lookup"><span data-stu-id="f7168-307">*-From* \<String></span></span>            | <span data-ttu-id="f7168-308">Migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="f7168-308">The starting migration.</span></span> <span data-ttu-id="f7168-309">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="f7168-309">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="f7168-310">Il numero 0 è un caso speciale che indica *prima della prima migrazione*.</span><span class="sxs-lookup"><span data-stu-id="f7168-310">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="f7168-311">Il valore predefinito è 0.</span><span class="sxs-lookup"><span data-stu-id="f7168-311">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="f7168-312">*Da a*\<String></span><span class="sxs-lookup"><span data-stu-id="f7168-312">*-To* \<String></span></span>              | <span data-ttu-id="f7168-313">Migrazione finale.</span><span class="sxs-lookup"><span data-stu-id="f7168-313">The ending migration.</span></span> <span data-ttu-id="f7168-314">L'impostazione predefinita è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-314">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="f7168-315">-Idempotente</span><span class="sxs-lookup"><span data-stu-id="f7168-315">-Idempotent</span></span>                  | <span data-ttu-id="f7168-316">Genera uno script che può essere utilizzato in un database in qualsiasi migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-316">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="f7168-317"><nobr>-Transactions</nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-317"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="f7168-318">Non generare istruzioni SQL Transaction.</span><span class="sxs-lookup"><span data-stu-id="f7168-318">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="f7168-319">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-319">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="f7168-320">-Output \<String></span><span class="sxs-lookup"><span data-stu-id="f7168-320">-Output \<String></span></span>            | <span data-ttu-id="f7168-321">File in cui scrivere il risultato.</span><span class="sxs-lookup"><span data-stu-id="f7168-321">The file to write the result to.</span></span> <span data-ttu-id="f7168-322">Se questo parametro viene omesso, il file viene creato con un nome generato nella stessa cartella in cui vengono creati i file di runtime dell'applicazione, ad esempio: */obj/debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="f7168-322">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="f7168-323">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-323">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="f7168-324">I parametri to, from e output supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="f7168-324">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="f7168-325">Nell'esempio seguente viene creato uno script per la migrazione di InitialCreate, usando il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-325">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="f7168-326">Nell'esempio seguente viene creato uno script per tutte le migrazioni dopo la migrazione di InitialCreate, usando l'ID di migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-326">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="f7168-327">Update-database</span><span class="sxs-lookup"><span data-stu-id="f7168-327">Update-Database</span></span>

<span data-ttu-id="f7168-328">Aggiorna il database all'ultima migrazione o a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="f7168-328">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="f7168-329">Parametro</span><span class="sxs-lookup"><span data-stu-id="f7168-329">Parameter</span></span>                           | <span data-ttu-id="f7168-330">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f7168-330">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f7168-331"><nobr>*-Migrazione*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-331"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="f7168-332">Migrazione di destinazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-332">The target migration.</span></span> <span data-ttu-id="f7168-333">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="f7168-333">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="f7168-334">Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="f7168-334">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="f7168-335">Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-335">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="f7168-336"><nobr>-Connessione \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f7168-336"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="f7168-337">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="f7168-337">The connection string to the database.</span></span> <span data-ttu-id="f7168-338">Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="f7168-338">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="f7168-339">Aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f7168-339">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="f7168-340">I [parametri comuni](#common-parameters) sono elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f7168-340">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="f7168-341">Il parametro Migration supporta l'espansione tramite tabulazione.</span><span class="sxs-lookup"><span data-stu-id="f7168-341">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="f7168-342">Nell'esempio seguente vengono ripristinate tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="f7168-342">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="f7168-343">Gli esempi seguenti aggiornano il database a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="f7168-343">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="f7168-344">Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:</span><span class="sxs-lookup"><span data-stu-id="f7168-344">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="f7168-345">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f7168-345">Additional resources</span></span>

* [<span data-ttu-id="f7168-346">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="f7168-346">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="f7168-347">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="f7168-347">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
