---
title: Guida di riferimento agli strumenti di EF Core (console di gestione pacchetti)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 698a2cadadb1389f2e659e3ecab2fb21d020322e
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672929"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="554ef-102">Guida di riferimento agli strumenti di Entity Framework Core-Console di gestione pacchetti in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="554ef-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="554ef-103">Gli strumenti della console di gestione pacchetti (PMC) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="554ef-104">Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), applicano migrazioni e generano codice per un modello basato su un database esistente.</span><span class="sxs-lookup"><span data-stu-id="554ef-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="554ef-105">I comandi vengono eseguiti all'interno di Visual Studio tramite la [console di gestione pacchetti](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="554ef-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="554ef-106">Questi strumenti funzionano sia con i progetti .NET Framework che con i progetti .NET Core.</span><span class="sxs-lookup"><span data-stu-id="554ef-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="554ef-107">Se non si usa Visual Studio, è consigliabile usare gli [strumenti da riga di comando EF Core](dotnet.md) .</span><span class="sxs-lookup"><span data-stu-id="554ef-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="554ef-108">Gli strumenti dell'interfaccia della riga di comando sono multipiattaforma ed eseguiti all'interno di un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="554ef-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="554ef-109">Installazione degli strumenti</span><span class="sxs-lookup"><span data-stu-id="554ef-109">Installing the tools</span></span>

<span data-ttu-id="554ef-110">Le procedure per l'installazione e l'aggiornamento degli strumenti sono diverse tra ASP.NET Core 2.1 + e le versioni precedenti o altri tipi di progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="554ef-111">ASP.NET Core versione 2,1 e successive</span><span class="sxs-lookup"><span data-stu-id="554ef-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="554ef-112">Gli strumenti vengono inclusi automaticamente in un progetto ASP.NET Core 2.1 + perché il `Microsoft.EntityFrameworkCore.Tools` pacchetto è incluso nel [metapacchetto Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="554ef-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="554ef-113">Pertanto, non è necessario eseguire alcuna operazione per installare gli strumenti, ma è necessario:</span><span class="sxs-lookup"><span data-stu-id="554ef-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="554ef-114">Ripristinare i pacchetti prima di utilizzare gli strumenti in un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="554ef-115">Installare un pacchetto per aggiornare gli strumenti a una versione più recente.</span><span class="sxs-lookup"><span data-stu-id="554ef-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="554ef-116">Per assicurarsi che si stia ricevendo la versione più recente degli strumenti, è consigliabile eseguire anche il passaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="554ef-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="554ef-117">Modificare il file con *estensione csproj* e aggiungere una riga che specifichi la versione più recente del pacchetto [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="554ef-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="554ef-118">Ad esempio, il file con *estensione csproj* potrebbe includere un oggetto `ItemGroup` simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="554ef-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="554ef-119">Aggiornare gli strumenti quando si riceve un messaggio simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="554ef-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="554ef-120">La versione di EF Core Tools ' 2.1.1-RTM-30846' è antecedente a quella del runtime ' 2.1.3-RTM-32065'.</span><span class="sxs-lookup"><span data-stu-id="554ef-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="554ef-121">Aggiornare gli strumenti per le funzionalità e le correzioni di bug più recenti.</span><span class="sxs-lookup"><span data-stu-id="554ef-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="554ef-122">Per aggiornare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="554ef-122">To update the tools:</span></span>

* <span data-ttu-id="554ef-123">installare la versione più recente di .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="554ef-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="554ef-124">Aggiornare Visual Studio alla versione più recente.</span><span class="sxs-lookup"><span data-stu-id="554ef-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="554ef-125">Modificare il file con *estensione csproj* in modo da includere un riferimento al pacchetto degli strumenti più recenti, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="554ef-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="554ef-126">Altre versioni e tipi di progetto</span><span class="sxs-lookup"><span data-stu-id="554ef-126">Other versions and project types</span></span>

<span data-ttu-id="554ef-127">Installare gli strumenti della console di gestione pacchetti eseguendo il comando seguente nella **console di gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="554ef-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="554ef-128">Aggiornare gli strumenti eseguendo il comando seguente nella **console di gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="554ef-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="554ef-129">Verificare l'installazione</span><span class="sxs-lookup"><span data-stu-id="554ef-129">Verify the installation</span></span>

<span data-ttu-id="554ef-130">Verificare che gli strumenti siano installati eseguendo questo comando:</span><span class="sxs-lookup"><span data-stu-id="554ef-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="554ef-131">L'output ha un aspetto simile al seguente (non indica la versione degli strumenti in uso):</span><span class="sxs-lookup"><span data-stu-id="554ef-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="554ef-132">Uso degli strumenti</span><span class="sxs-lookup"><span data-stu-id="554ef-132">Using the tools</span></span>

<span data-ttu-id="554ef-133">Prima di usare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="554ef-133">Before using the tools:</span></span>

* <span data-ttu-id="554ef-134">Comprendere la differenza tra il progetto di destinazione e quello di avvio.</span><span class="sxs-lookup"><span data-stu-id="554ef-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="554ef-135">Informazioni su come usare gli strumenti con le librerie di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="554ef-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="554ef-136">Per ASP.NET Core progetti, impostare l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="554ef-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="554ef-137">Progetto di destinazione e di avvio</span><span class="sxs-lookup"><span data-stu-id="554ef-137">Target and startup project</span></span>

<span data-ttu-id="554ef-138">I comandi fanno riferimento a un *progetto e a* un *progetto di avvio*.</span><span class="sxs-lookup"><span data-stu-id="554ef-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="554ef-139">Il *progetto* è anche noto come *progetto di destinazione* perché è il punto in cui i comandi aggiungono o rimuovono i file.</span><span class="sxs-lookup"><span data-stu-id="554ef-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="554ef-140">Per impostazione predefinita, il progetto **predefinito** selezionato nella **console di gestione pacchetti** è il progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="554ef-141">È possibile specificare un progetto diverso come progetto di destinazione utilizzando l' <nobr>`--project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="554ef-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="554ef-142">Il *progetto di avvio* è quello che gli strumenti compilano ed eseguono.</span><span class="sxs-lookup"><span data-stu-id="554ef-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="554ef-143">Gli strumenti di devono eseguire il codice dell'applicazione in fase di progettazione per ottenere informazioni sul progetto, ad esempio la stringa di connessione al database e la configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="554ef-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="554ef-144">Per impostazione predefinita, il **progetto di avvio** in **Esplora soluzioni** è il progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="554ef-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="554ef-145">È possibile specificare un progetto diverso come progetto di avvio usando l' <nobr>`--startup-project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="554ef-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="554ef-146">Il progetto di avvio e il progetto di destinazione sono spesso lo stesso progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="554ef-147">Uno scenario tipico in cui si trovano progetti distinti è quando:</span><span class="sxs-lookup"><span data-stu-id="554ef-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="554ef-148">Il contesto EF Core e le classi di entità si trovano in una libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="554ef-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="554ef-149">Un'app console o Web .NET Core fa riferimento alla libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="554ef-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="554ef-150">È anche possibile [inserire il codice delle migrazioni in una libreria di classi separata dal contesto EF Core](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="554ef-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="554ef-151">Altri Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="554ef-151">Other target frameworks</span></span>

<span data-ttu-id="554ef-152">Gli strumenti della console di gestione pacchetti funzionano con i progetti .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="554ef-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="554ef-153">Le app che hanno il modello di EF Core in una libreria di classi .NET Standard potrebbero non avere un progetto .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="554ef-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="554ef-154">Ad esempio, questo vale per le app Novell e piattaforma UWP (Universal Windows Platform).</span><span class="sxs-lookup"><span data-stu-id="554ef-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="554ef-155">In questi casi, è possibile creare un progetto di app console .NET Core o .NET Framework il cui unico scopo è fungere da progetto di avvio per gli strumenti di.</span><span class="sxs-lookup"><span data-stu-id="554ef-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="554ef-156">Il progetto può essere un progetto fittizio senza codice reale &mdash; necessario per fornire una destinazione per gli strumenti.</span><span class="sxs-lookup"><span data-stu-id="554ef-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="554ef-157">Perché è necessario un progetto fittizio?</span><span class="sxs-lookup"><span data-stu-id="554ef-157">Why is a dummy project required?</span></span> <span data-ttu-id="554ef-158">Come indicato in precedenza, gli strumenti devono eseguire il codice dell'applicazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="554ef-159">A tale scopo, è necessario usare .NET Core o .NET Framework Runtime.</span><span class="sxs-lookup"><span data-stu-id="554ef-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="554ef-160">Quando il modello di EF Core si trova in un progetto destinato a .NET Core o .NET Framework, gli strumenti di EF Core prendono in prestito il runtime dal progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="554ef-161">Questa operazione non può essere eseguita se il modello di EF Core si trova in una libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="554ef-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="554ef-162">Il .NET Standard non è un'implementazione .NET effettiva; si tratta di una specifica di un set di API che le implementazioni di .NET devono supportare.</span><span class="sxs-lookup"><span data-stu-id="554ef-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="554ef-163">Pertanto .NET Standard non è sufficiente per gli strumenti EF Core per eseguire il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="554ef-164">Il progetto fittizio creato da usare come progetto di avvio fornisce una piattaforma di destinazione concreta in cui gli strumenti possono caricare la libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="554ef-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="554ef-165">Ambiente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="554ef-165">ASP.NET Core environment</span></span>

<span data-ttu-id="554ef-166">Per specificare l'ambiente per i progetti ASP.NET Core, impostare **env: ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.</span><span class="sxs-lookup"><span data-stu-id="554ef-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="554ef-167">Parametri comuni</span><span class="sxs-lookup"><span data-stu-id="554ef-167">Common parameters</span></span>

<span data-ttu-id="554ef-168">La tabella seguente illustra i parametri comuni a tutti i comandi EF Core:</span><span class="sxs-lookup"><span data-stu-id="554ef-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="554ef-169">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-169">Parameter</span></span>                 | <span data-ttu-id="554ef-170">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-171">-Stringa di contesto \<></span><span class="sxs-lookup"><span data-stu-id="554ef-171">-Context \<String></span></span>        | <span data-ttu-id="554ef-172">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="554ef-172">The `DbContext` class to use.</span></span> <span data-ttu-id="554ef-173">Solo il nome della classe o completo con gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="554ef-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="554ef-174">Se questo parametro viene omesso, EF Core trova la classe del contesto.</span><span class="sxs-lookup"><span data-stu-id="554ef-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="554ef-175">Se sono presenti più classi di contesto, questo parametro è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="554ef-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="554ef-176">- \<> stringa di progetto</span><span class="sxs-lookup"><span data-stu-id="554ef-176">-Project \<String></span></span>        | <span data-ttu-id="554ef-177">Progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-177">The target project.</span></span> <span data-ttu-id="554ef-178">Se questo parametro viene omesso, come progetto di destinazione viene utilizzato il **progetto predefinito** per la **console di gestione pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="554ef-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="554ef-179"><nobr>-Progetto</nobr> \<> stringa</span><span class="sxs-lookup"><span data-stu-id="554ef-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="554ef-180">Progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="554ef-180">The startup project.</span></span> <span data-ttu-id="554ef-181">Se questo parametro viene omesso, il **progetto di avvio** nelle **proprietà della soluzione** viene utilizzato come progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="554ef-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="554ef-182">-Verbose</span></span>                  | <span data-ttu-id="554ef-183">Mostra output dettagliato.</span><span class="sxs-lookup"><span data-stu-id="554ef-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="554ef-184">Per visualizzare le informazioni della guida relative a un comando, usare il comando di PowerShell `Get-Help` .</span><span class="sxs-lookup"><span data-stu-id="554ef-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="554ef-185">Il contesto, il progetto e i parametri progetto supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="554ef-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="554ef-186">Aggiungi-migrazione</span><span class="sxs-lookup"><span data-stu-id="554ef-186">Add-Migration</span></span>

<span data-ttu-id="554ef-187">Aggiunge una nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-187">Adds a new migration.</span></span>

<span data-ttu-id="554ef-188">Parametri</span><span class="sxs-lookup"><span data-stu-id="554ef-188">Parameters:</span></span>

| <span data-ttu-id="554ef-189">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-189">Parameter</span></span>                         | <span data-ttu-id="554ef-190">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-191"><nobr>-Name \< String><nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="554ef-192">Nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-192">The name of the migration.</span></span> <span data-ttu-id="554ef-193">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="554ef-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="554ef-194"><nobr>-OutputDir \< stringa></nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="554ef-195">Directory usata per l'output dei file.</span><span class="sxs-lookup"><span data-stu-id="554ef-195">The directory use to output the files.</span></span> <span data-ttu-id="554ef-196">I percorsi sono relativi alla directory del progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="554ef-197">Il valore predefinito è "Migrations".</span><span class="sxs-lookup"><span data-stu-id="554ef-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="554ef-198"><nobr>->stringa dello spazio dei nomi \<</nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="554ef-199">Spazio dei nomi da utilizzare per le classi generate.</span><span class="sxs-lookup"><span data-stu-id="554ef-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="554ef-200">Il valore predefinito è generato dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="554ef-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="554ef-201">Drop-database</span><span class="sxs-lookup"><span data-stu-id="554ef-201">Drop-Database</span></span>

<span data-ttu-id="554ef-202">Elimina il database.</span><span class="sxs-lookup"><span data-stu-id="554ef-202">Drops the database.</span></span>

<span data-ttu-id="554ef-203">Parametri</span><span class="sxs-lookup"><span data-stu-id="554ef-203">Parameters:</span></span>

| <span data-ttu-id="554ef-204">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-204">Parameter</span></span> | <span data-ttu-id="554ef-205">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="554ef-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="554ef-206">-WhatIf</span></span>   | <span data-ttu-id="554ef-207">Indica il database da eliminare, ma non eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="554ef-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="554ef-208">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="554ef-208">Get-DbContext</span></span>

<span data-ttu-id="554ef-209">Ottiene informazioni su un `DbContext` tipo.</span><span class="sxs-lookup"><span data-stu-id="554ef-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="554ef-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="554ef-210">Remove-Migration</span></span>

<span data-ttu-id="554ef-211">Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).</span><span class="sxs-lookup"><span data-stu-id="554ef-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="554ef-212">Parametri</span><span class="sxs-lookup"><span data-stu-id="554ef-212">Parameters:</span></span>

| <span data-ttu-id="554ef-213">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-213">Parameter</span></span> | <span data-ttu-id="554ef-214">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-215">-Force</span><span class="sxs-lookup"><span data-stu-id="554ef-215">-Force</span></span>    | <span data-ttu-id="554ef-216">Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database.</span><span class="sxs-lookup"><span data-stu-id="554ef-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="554ef-217">Impalcature-DbContext</span><span class="sxs-lookup"><span data-stu-id="554ef-217">Scaffold-DbContext</span></span>

<span data-ttu-id="554ef-218">Genera il codice per un `DbContext` tipo di entità e per un database.</span><span class="sxs-lookup"><span data-stu-id="554ef-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="554ef-219">Per `Scaffold-DbContext` poter generare un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="554ef-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="554ef-220">Parametri</span><span class="sxs-lookup"><span data-stu-id="554ef-220">Parameters:</span></span>

| <span data-ttu-id="554ef-221">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-221">Parameter</span></span>                          | <span data-ttu-id="554ef-222">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-223"><nobr>-Stringa di connessione \<></nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="554ef-224">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="554ef-224">The connection string to the database.</span></span> <span data-ttu-id="554ef-225">Per i progetti ASP.NET Core 2. x, il valore può essere *nome = \< nome della stringa di connessione>*.</span><span class="sxs-lookup"><span data-stu-id="554ef-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="554ef-226">In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="554ef-227">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="554ef-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="554ef-228"><nobr>- \<>stringa del provider</nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="554ef-229">Provider da usare.</span><span class="sxs-lookup"><span data-stu-id="554ef-229">The provider to use.</span></span> <span data-ttu-id="554ef-230">Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="554ef-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="554ef-231">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="554ef-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="554ef-232">-OutputDir \< stringa></span><span class="sxs-lookup"><span data-stu-id="554ef-232">-OutputDir \<String></span></span>               | <span data-ttu-id="554ef-233">Directory in cui inserire i file.</span><span class="sxs-lookup"><span data-stu-id="554ef-233">The directory to put files in.</span></span> <span data-ttu-id="554ef-234">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="554ef-235">-ContextDir \< stringa></span><span class="sxs-lookup"><span data-stu-id="554ef-235">-ContextDir \<String></span></span>              | <span data-ttu-id="554ef-236">Directory in cui inserire il `DbContext` file.</span><span class="sxs-lookup"><span data-stu-id="554ef-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="554ef-237">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="554ef-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="554ef-238">-> stringa dello spazio dei nomi \<</span><span class="sxs-lookup"><span data-stu-id="554ef-238">-Namespace \<String></span></span>               | <span data-ttu-id="554ef-239">Spazio dei nomi da utilizzare per tutte le classi generate.</span><span class="sxs-lookup"><span data-stu-id="554ef-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="554ef-240">Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="554ef-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="554ef-241">-ContextNamespace \< stringa></span><span class="sxs-lookup"><span data-stu-id="554ef-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="554ef-242">Spazio dei nomi da utilizzare per la `DbContext` classe generata.</span><span class="sxs-lookup"><span data-stu-id="554ef-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="554ef-243">Nota: esegue l'override di `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="554ef-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="554ef-244">-Stringa di contesto \<></span><span class="sxs-lookup"><span data-stu-id="554ef-244">-Context \<String></span></span>                 | <span data-ttu-id="554ef-245">Nome della `DbContext` classe da generare.</span><span class="sxs-lookup"><span data-stu-id="554ef-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="554ef-246">-Schemas \< String [] ></span><span class="sxs-lookup"><span data-stu-id="554ef-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="554ef-247">Schemi delle tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="554ef-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="554ef-248">Se questo parametro viene omesso, vengono inclusi tutti gli schemi.</span><span class="sxs-lookup"><span data-stu-id="554ef-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="554ef-249">-Tables \< String [] ></span><span class="sxs-lookup"><span data-stu-id="554ef-249">-Tables \<String[]></span></span>                | <span data-ttu-id="554ef-250">Tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="554ef-250">The tables to generate entity types for.</span></span> <span data-ttu-id="554ef-251">Se questo parametro viene omesso, vengono incluse tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="554ef-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="554ef-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="554ef-252">-DataAnnotations</span></span>                   | <span data-ttu-id="554ef-253">Utilizzare gli attributi per configurare il modello (laddove possibile).</span><span class="sxs-lookup"><span data-stu-id="554ef-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="554ef-254">Se questo parametro viene omesso, viene usata solo l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="554ef-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="554ef-255">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="554ef-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="554ef-256">Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database.</span><span class="sxs-lookup"><span data-stu-id="554ef-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="554ef-257">Se questo parametro viene omesso, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#.</span><span class="sxs-lookup"><span data-stu-id="554ef-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="554ef-258">-Force</span><span class="sxs-lookup"><span data-stu-id="554ef-258">-Force</span></span>                             | <span data-ttu-id="554ef-259">Sovrascrivere i file esistenti.</span><span class="sxs-lookup"><span data-stu-id="554ef-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="554ef-260">Esempio:</span><span class="sxs-lookup"><span data-stu-id="554ef-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="554ef-261">Esempio che associa solo le tabelle selezionate e crea il contesto in una cartella separata con il nome e lo spazio dei nomi specificati:</span><span class="sxs-lookup"><span data-stu-id="554ef-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="554ef-262">Script-migrazione</span><span class="sxs-lookup"><span data-stu-id="554ef-262">Script-Migration</span></span>

<span data-ttu-id="554ef-263">Genera uno script SQL che applica tutte le modifiche a una migrazione selezionata a un'altra.</span><span class="sxs-lookup"><span data-stu-id="554ef-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="554ef-264">Parametri</span><span class="sxs-lookup"><span data-stu-id="554ef-264">Parameters:</span></span>

| <span data-ttu-id="554ef-265">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-265">Parameter</span></span>                | <span data-ttu-id="554ef-266">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-267">*-Da* \<> stringa</span><span class="sxs-lookup"><span data-stu-id="554ef-267">*-From* \<String></span></span>        | <span data-ttu-id="554ef-268">Migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="554ef-268">The starting migration.</span></span> <span data-ttu-id="554ef-269">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="554ef-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="554ef-270">Il numero 0 è un caso speciale che indica *prima della prima migrazione*.</span><span class="sxs-lookup"><span data-stu-id="554ef-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="554ef-271">Il valore predefinito è 0.</span><span class="sxs-lookup"><span data-stu-id="554ef-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="554ef-272">*Da a* \<> stringa</span><span class="sxs-lookup"><span data-stu-id="554ef-272">*-To* \<String></span></span>          | <span data-ttu-id="554ef-273">Migrazione finale.</span><span class="sxs-lookup"><span data-stu-id="554ef-273">The ending migration.</span></span> <span data-ttu-id="554ef-274">L'impostazione predefinita è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="554ef-275"><nobr>-Idempotente</nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="554ef-276">Genera uno script che può essere utilizzato in un database in qualsiasi migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="554ef-277">-Stringa di output \<></span><span class="sxs-lookup"><span data-stu-id="554ef-277">-Output \<String></span></span>        | <span data-ttu-id="554ef-278">File in cui scrivere il risultato.</span><span class="sxs-lookup"><span data-stu-id="554ef-278">The file to write the result to.</span></span> <span data-ttu-id="554ef-279">Se questo parametro viene omesso, il file viene creato con un nome generato nella stessa cartella in cui vengono creati i file di runtime dell'applicazione, ad esempio: */obj/debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="554ef-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="554ef-280">I parametri to, from e output supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="554ef-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="554ef-281">Nell'esempio seguente viene creato uno script per la migrazione di InitialCreate, usando il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="554ef-282">Nell'esempio seguente viene creato uno script per tutte le migrazioni dopo la migrazione di InitialCreate, usando l'ID di migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="554ef-283">Update-database</span><span class="sxs-lookup"><span data-stu-id="554ef-283">Update-Database</span></span>

<span data-ttu-id="554ef-284">Aggiorna il database all'ultima migrazione o a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="554ef-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="554ef-285">Parametro</span><span class="sxs-lookup"><span data-stu-id="554ef-285">Parameter</span></span>                           | <span data-ttu-id="554ef-286">Descrizione</span><span class="sxs-lookup"><span data-stu-id="554ef-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="554ef-287"><nobr>*-Migrazione* \<>stringa</nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="554ef-288">Migrazione di destinazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-288">The target migration.</span></span> <span data-ttu-id="554ef-289">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="554ef-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="554ef-290">Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="554ef-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="554ef-291">Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="554ef-292"><nobr>-Stringa di connessione \<></nobr></span><span class="sxs-lookup"><span data-stu-id="554ef-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="554ef-293">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="554ef-293">The connection string to the database.</span></span> <span data-ttu-id="554ef-294">Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="554ef-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="554ef-295">Il parametro Migration supporta l'espansione tramite tabulazione.</span><span class="sxs-lookup"><span data-stu-id="554ef-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="554ef-296">Nell'esempio seguente vengono ripristinate tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="554ef-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="554ef-297">Gli esempi seguenti aggiornano il database a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="554ef-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="554ef-298">Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:</span><span class="sxs-lookup"><span data-stu-id="554ef-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="554ef-299">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="554ef-299">Additional resources</span></span>

* [<span data-ttu-id="554ef-300">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="554ef-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="554ef-301">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="554ef-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
