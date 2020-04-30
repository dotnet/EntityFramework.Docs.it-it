---
title: Guida di riferimento agli strumenti di EF Core (console di gestione pacchetti)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538397"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="38ec6-102">Guida di riferimento agli strumenti di Entity Framework Core-Console di gestione pacchetti in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38ec6-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="38ec6-103">Gli strumenti della console di gestione pacchetti (PMC) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="38ec6-104">Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), applicano migrazioni e generano codice per un modello basato su un database esistente.</span><span class="sxs-lookup"><span data-stu-id="38ec6-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="38ec6-105">I comandi vengono eseguiti all'interno di Visual Studio tramite la [console di gestione pacchetti](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="38ec6-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="38ec6-106">Questi strumenti funzionano sia con i progetti .NET Framework che con i progetti .NET Core.</span><span class="sxs-lookup"><span data-stu-id="38ec6-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="38ec6-107">Se non si usa Visual Studio, è consigliabile usare gli [strumenti da riga di comando EF Core](dotnet.md) .</span><span class="sxs-lookup"><span data-stu-id="38ec6-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="38ec6-108">Gli strumenti dell'interfaccia della riga di comando sono multipiattaforma ed eseguiti all'interno di un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="38ec6-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="38ec6-109">Installazione degli strumenti</span><span class="sxs-lookup"><span data-stu-id="38ec6-109">Installing the tools</span></span>

<span data-ttu-id="38ec6-110">Le procedure per l'installazione e l'aggiornamento degli strumenti sono diverse tra ASP.NET Core 2.1 + e le versioni precedenti o altri tipi di progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="38ec6-111">ASP.NET Core versione 2,1 e successive</span><span class="sxs-lookup"><span data-stu-id="38ec6-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="38ec6-112">Gli strumenti vengono inclusi automaticamente in un progetto ASP.NET Core 2.1 + perché il `Microsoft.EntityFrameworkCore.Tools` pacchetto è incluso nel [metapacchetto Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="38ec6-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="38ec6-113">Pertanto, non è necessario eseguire alcuna operazione per installare gli strumenti, ma è necessario:</span><span class="sxs-lookup"><span data-stu-id="38ec6-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="38ec6-114">Ripristinare i pacchetti prima di utilizzare gli strumenti in un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="38ec6-115">Installare un pacchetto per aggiornare gli strumenti a una versione più recente.</span><span class="sxs-lookup"><span data-stu-id="38ec6-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="38ec6-116">Per assicurarsi che si stia ricevendo la versione più recente degli strumenti, è consigliabile eseguire anche il passaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="38ec6-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="38ec6-117">Modificare il file con *estensione csproj* e aggiungere una riga che specifichi la versione più recente del pacchetto [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="38ec6-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="38ec6-118">Ad esempio, il file con *estensione csproj* potrebbe includere `ItemGroup` un oggetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="38ec6-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="38ec6-119">Aggiornare gli strumenti quando si riceve un messaggio simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="38ec6-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="38ec6-120">La versione di EF Core Tools ' 2.1.1-RTM-30846' è antecedente a quella del runtime ' 2.1.3-RTM-32065'.</span><span class="sxs-lookup"><span data-stu-id="38ec6-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="38ec6-121">Aggiornare gli strumenti per le funzionalità e le correzioni di bug più recenti.</span><span class="sxs-lookup"><span data-stu-id="38ec6-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="38ec6-122">Per aggiornare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="38ec6-122">To update the tools:</span></span>

* <span data-ttu-id="38ec6-123">installare la versione più recente di .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="38ec6-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="38ec6-124">Aggiornare Visual Studio alla versione più recente.</span><span class="sxs-lookup"><span data-stu-id="38ec6-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="38ec6-125">Modificare il file con *estensione csproj* in modo da includere un riferimento al pacchetto degli strumenti più recenti, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="38ec6-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="38ec6-126">Altre versioni e tipi di progetto</span><span class="sxs-lookup"><span data-stu-id="38ec6-126">Other versions and project types</span></span>

<span data-ttu-id="38ec6-127">Installare gli strumenti della console di gestione pacchetti eseguendo il comando seguente nella **console di gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="38ec6-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="38ec6-128">Aggiornare gli strumenti eseguendo il comando seguente nella **console di gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="38ec6-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="38ec6-129">Verificare l'installazione</span><span class="sxs-lookup"><span data-stu-id="38ec6-129">Verify the installation</span></span>

<span data-ttu-id="38ec6-130">Verificare che gli strumenti siano installati eseguendo questo comando:</span><span class="sxs-lookup"><span data-stu-id="38ec6-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="38ec6-131">L'output ha un aspetto simile al seguente (non indica la versione degli strumenti in uso):</span><span class="sxs-lookup"><span data-stu-id="38ec6-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="38ec6-132">Uso degli strumenti</span><span class="sxs-lookup"><span data-stu-id="38ec6-132">Using the tools</span></span>

<span data-ttu-id="38ec6-133">Prima di usare gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="38ec6-133">Before using the tools:</span></span>

* <span data-ttu-id="38ec6-134">Comprendere la differenza tra il progetto di destinazione e quello di avvio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="38ec6-135">Informazioni su come usare gli strumenti con le librerie di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="38ec6-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="38ec6-136">Per ASP.NET Core progetti, impostare l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="38ec6-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="38ec6-137">Progetto di destinazione e di avvio</span><span class="sxs-lookup"><span data-stu-id="38ec6-137">Target and startup project</span></span>

<span data-ttu-id="38ec6-138">I comandi fanno riferimento a un *progetto e a* un *progetto di avvio*.</span><span class="sxs-lookup"><span data-stu-id="38ec6-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="38ec6-139">Il *progetto* è anche noto come *progetto di destinazione* perché è il punto in cui i comandi aggiungono o rimuovono i file.</span><span class="sxs-lookup"><span data-stu-id="38ec6-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="38ec6-140">Per impostazione predefinita, il progetto **predefinito** selezionato nella **console di gestione pacchetti** è il progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="38ec6-141">È possibile specificare un progetto diverso come progetto di destinazione utilizzando l' <nobr>`--project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="38ec6-142">Il *progetto di avvio* è quello che gli strumenti compilano ed eseguono.</span><span class="sxs-lookup"><span data-stu-id="38ec6-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="38ec6-143">Gli strumenti di devono eseguire il codice dell'applicazione in fase di progettazione per ottenere informazioni sul progetto, ad esempio la stringa di connessione al database e la configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="38ec6-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="38ec6-144">Per impostazione predefinita, il **progetto di avvio** in **Esplora soluzioni** è il progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="38ec6-145">È possibile specificare un progetto diverso come progetto di avvio usando l' <nobr>`--startup-project`</nobr> opzione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="38ec6-146">Il progetto di avvio e il progetto di destinazione sono spesso lo stesso progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="38ec6-147">Uno scenario tipico in cui si trovano progetti distinti è quando:</span><span class="sxs-lookup"><span data-stu-id="38ec6-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="38ec6-148">Il contesto EF Core e le classi di entità si trovano in una libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="38ec6-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="38ec6-149">Un'app console o Web .NET Core fa riferimento alla libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="38ec6-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="38ec6-150">È anche possibile [inserire il codice delle migrazioni in una libreria di classi separata dal contesto EF Core](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="38ec6-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="38ec6-151">Altri Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="38ec6-151">Other target frameworks</span></span>

<span data-ttu-id="38ec6-152">Gli strumenti della console di gestione pacchetti funzionano con i progetti .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="38ec6-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="38ec6-153">Le app che hanno il modello di EF Core in una libreria di classi .NET Standard potrebbero non avere un progetto .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="38ec6-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="38ec6-154">Ad esempio, questo vale per le app Novell e piattaforma UWP (Universal Windows Platform).</span><span class="sxs-lookup"><span data-stu-id="38ec6-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="38ec6-155">In questi casi, è possibile creare un progetto di app console .NET Core o .NET Framework il cui unico scopo è fungere da progetto di avvio per gli strumenti di.</span><span class="sxs-lookup"><span data-stu-id="38ec6-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="38ec6-156">Il progetto può essere un progetto fittizio senza codice &mdash; reale necessario per fornire una destinazione per gli strumenti.</span><span class="sxs-lookup"><span data-stu-id="38ec6-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="38ec6-157">Perché è necessario un progetto fittizio?</span><span class="sxs-lookup"><span data-stu-id="38ec6-157">Why is a dummy project required?</span></span> <span data-ttu-id="38ec6-158">Come indicato in precedenza, gli strumenti devono eseguire il codice dell'applicazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="38ec6-159">A tale scopo, è necessario usare .NET Core o .NET Framework Runtime.</span><span class="sxs-lookup"><span data-stu-id="38ec6-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="38ec6-160">Quando il modello di EF Core si trova in un progetto destinato a .NET Core o .NET Framework, gli strumenti di EF Core prendono in prestito il runtime dal progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="38ec6-161">Questa operazione non può essere eseguita se il modello di EF Core si trova in una libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="38ec6-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="38ec6-162">Il .NET Standard non è un'implementazione .NET effettiva; si tratta di una specifica di un set di API che le implementazioni di .NET devono supportare.</span><span class="sxs-lookup"><span data-stu-id="38ec6-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="38ec6-163">Pertanto .NET Standard non è sufficiente per gli strumenti EF Core per eseguire il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="38ec6-164">Il progetto fittizio creato da usare come progetto di avvio fornisce una piattaforma di destinazione concreta in cui gli strumenti possono caricare la libreria di classi .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="38ec6-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="38ec6-165">Ambiente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38ec6-165">ASP.NET Core environment</span></span>

<span data-ttu-id="38ec6-166">Per specificare l'ambiente per i progetti ASP.NET Core, impostare **env: ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.</span><span class="sxs-lookup"><span data-stu-id="38ec6-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="38ec6-167">Parametri comuni</span><span class="sxs-lookup"><span data-stu-id="38ec6-167">Common parameters</span></span>

<span data-ttu-id="38ec6-168">La tabella seguente illustra i parametri comuni a tutti i comandi EF Core:</span><span class="sxs-lookup"><span data-stu-id="38ec6-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="38ec6-169">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-169">Parameter</span></span>                 | <span data-ttu-id="38ec6-170">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-171">-Stringa \<di contesto></span><span class="sxs-lookup"><span data-stu-id="38ec6-171">-Context \<String></span></span>        | <span data-ttu-id="38ec6-172">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="38ec6-172">The `DbContext` class to use.</span></span> <span data-ttu-id="38ec6-173">Solo il nome della classe o completo con gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="38ec6-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="38ec6-174">Se questo parametro viene omesso, EF Core trova la classe del contesto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="38ec6-175">Se sono presenti più classi di contesto, questo parametro è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="38ec6-176">-> \<stringa di progetto</span><span class="sxs-lookup"><span data-stu-id="38ec6-176">-Project \<String></span></span>        | <span data-ttu-id="38ec6-177">Progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-177">The target project.</span></span> <span data-ttu-id="38ec6-178">Se questo parametro viene omesso, come progetto di destinazione viene utilizzato il **progetto predefinito** per la **console di gestione pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="38ec6-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="38ec6-179"><nobr>-Progetto</nobr> \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="38ec6-180">Progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-180">The startup project.</span></span> <span data-ttu-id="38ec6-181">Se questo parametro viene omesso, il **progetto di avvio** nelle **proprietà della soluzione** viene utilizzato come progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="38ec6-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="38ec6-182">-Verbose</span></span>                  | <span data-ttu-id="38ec6-183">Mostra output dettagliato.</span><span class="sxs-lookup"><span data-stu-id="38ec6-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="38ec6-184">Per visualizzare le informazioni della guida relative a un comando, `Get-Help` usare il comando di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="38ec6-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="38ec6-185">Il contesto, il progetto e i parametri progetto supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="38ec6-186">Aggiungi-migrazione</span><span class="sxs-lookup"><span data-stu-id="38ec6-186">Add-Migration</span></span>

<span data-ttu-id="38ec6-187">Aggiunge una nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-187">Adds a new migration.</span></span>

<span data-ttu-id="38ec6-188">Parametri</span><span class="sxs-lookup"><span data-stu-id="38ec6-188">Parameters:</span></span>

| <span data-ttu-id="38ec6-189">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-189">Parameter</span></span>                         | <span data-ttu-id="38ec6-190">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-191"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="38ec6-192">Nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-192">The name of the migration.</span></span> <span data-ttu-id="38ec6-193">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="38ec6-194"><nobr>-OutputDir \<stringa></nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="38ec6-195">Directory usata per l'output dei file.</span><span class="sxs-lookup"><span data-stu-id="38ec6-195">The directory use to output the files.</span></span> <span data-ttu-id="38ec6-196">I percorsi sono relativi alla directory del progetto di destinazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="38ec6-197">Il valore predefinito è "Migrations".</span><span class="sxs-lookup"><span data-stu-id="38ec6-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="38ec6-198"><nobr>->\<stringa dello spazio dei nomi</nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="38ec6-199">Spazio dei nomi da utilizzare per le classi generate.</span><span class="sxs-lookup"><span data-stu-id="38ec6-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="38ec6-200">Il valore predefinito è generato dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="38ec6-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="38ec6-201">Drop-database</span><span class="sxs-lookup"><span data-stu-id="38ec6-201">Drop-Database</span></span>

<span data-ttu-id="38ec6-202">Elimina il database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-202">Drops the database.</span></span>

<span data-ttu-id="38ec6-203">Parametri</span><span class="sxs-lookup"><span data-stu-id="38ec6-203">Parameters:</span></span>

| <span data-ttu-id="38ec6-204">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-204">Parameter</span></span> | <span data-ttu-id="38ec6-205">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="38ec6-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="38ec6-206">-WhatIf</span></span>   | <span data-ttu-id="38ec6-207">Indica il database da eliminare, ma non eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="38ec6-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="38ec6-208">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="38ec6-208">Get-DbContext</span></span>

<span data-ttu-id="38ec6-209">Ottiene informazioni su un `DbContext` tipo.</span><span class="sxs-lookup"><span data-stu-id="38ec6-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="38ec6-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="38ec6-210">Remove-Migration</span></span>

<span data-ttu-id="38ec6-211">Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).</span><span class="sxs-lookup"><span data-stu-id="38ec6-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="38ec6-212">Parametri</span><span class="sxs-lookup"><span data-stu-id="38ec6-212">Parameters:</span></span>

| <span data-ttu-id="38ec6-213">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-213">Parameter</span></span> | <span data-ttu-id="38ec6-214">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-215">-Force</span><span class="sxs-lookup"><span data-stu-id="38ec6-215">-Force</span></span>    | <span data-ttu-id="38ec6-216">Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="38ec6-217">Impalcature-DbContext</span><span class="sxs-lookup"><span data-stu-id="38ec6-217">Scaffold-DbContext</span></span>

<span data-ttu-id="38ec6-218">Genera il codice per `DbContext` un tipo di entità e per un database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="38ec6-219">Per `Scaffold-DbContext` poter generare un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="38ec6-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="38ec6-220">Parametri</span><span class="sxs-lookup"><span data-stu-id="38ec6-220">Parameters:</span></span>

| <span data-ttu-id="38ec6-221">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-221">Parameter</span></span>                          | <span data-ttu-id="38ec6-222">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-223"><nobr>-Stringa \<di connessione></nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="38ec6-224">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-224">The connection string to the database.</span></span> <span data-ttu-id="38ec6-225">Per i progetti ASP.NET Core 2. x, il valore può essere *nome\<= nome della stringa di connessione>*.</span><span class="sxs-lookup"><span data-stu-id="38ec6-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="38ec6-226">In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="38ec6-227">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="38ec6-228"><nobr>->\<stringa del provider</nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="38ec6-229">Provider da usare.</span><span class="sxs-lookup"><span data-stu-id="38ec6-229">The provider to use.</span></span> <span data-ttu-id="38ec6-230">Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="38ec6-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="38ec6-231">Si tratta di un parametro posizionale ed è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="38ec6-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="38ec6-232">-OutputDir \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-232">-OutputDir \<String></span></span>               | <span data-ttu-id="38ec6-233">Directory in cui inserire i file.</span><span class="sxs-lookup"><span data-stu-id="38ec6-233">The directory to put files in.</span></span> <span data-ttu-id="38ec6-234">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="38ec6-235">-ContextDir \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-235">-ContextDir \<String></span></span>              | <span data-ttu-id="38ec6-236">Directory in cui inserire il `DbContext` file.</span><span class="sxs-lookup"><span data-stu-id="38ec6-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="38ec6-237">I percorsi sono relativi alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="38ec6-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="38ec6-238">-> \<stringa dello spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="38ec6-238">-Namespace \<String></span></span>               | <span data-ttu-id="38ec6-239">Spazio dei nomi da utilizzare per tutte le classi generate.</span><span class="sxs-lookup"><span data-stu-id="38ec6-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="38ec6-240">Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output.</span><span class="sxs-lookup"><span data-stu-id="38ec6-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="38ec6-241">-ContextNamespace \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="38ec6-242">Spazio dei nomi da utilizzare per la `DbContext` classe generata.</span><span class="sxs-lookup"><span data-stu-id="38ec6-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="38ec6-243">Nota: esegue `-Namespace`l'override di.</span><span class="sxs-lookup"><span data-stu-id="38ec6-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="38ec6-244">-Stringa \<di contesto></span><span class="sxs-lookup"><span data-stu-id="38ec6-244">-Context \<String></span></span>                 | <span data-ttu-id="38ec6-245">Nome della `DbContext` classe da generare.</span><span class="sxs-lookup"><span data-stu-id="38ec6-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="38ec6-246">-Schemas \<String [] ></span><span class="sxs-lookup"><span data-stu-id="38ec6-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="38ec6-247">Schemi delle tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="38ec6-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="38ec6-248">Se questo parametro viene omesso, vengono inclusi tutti gli schemi.</span><span class="sxs-lookup"><span data-stu-id="38ec6-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="38ec6-249">-Tables \<String [] ></span><span class="sxs-lookup"><span data-stu-id="38ec6-249">-Tables \<String[]></span></span>                | <span data-ttu-id="38ec6-250">Tabelle per cui generare i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="38ec6-250">The tables to generate entity types for.</span></span> <span data-ttu-id="38ec6-251">Se questo parametro viene omesso, vengono incluse tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="38ec6-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="38ec6-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="38ec6-252">-DataAnnotations</span></span>                   | <span data-ttu-id="38ec6-253">Utilizzare gli attributi per configurare il modello (laddove possibile).</span><span class="sxs-lookup"><span data-stu-id="38ec6-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="38ec6-254">Se questo parametro viene omesso, viene usata solo l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="38ec6-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="38ec6-255">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="38ec6-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="38ec6-256">Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="38ec6-257">Se questo parametro viene omesso, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#.</span><span class="sxs-lookup"><span data-stu-id="38ec6-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="38ec6-258">-Force</span><span class="sxs-lookup"><span data-stu-id="38ec6-258">-Force</span></span>                             | <span data-ttu-id="38ec6-259">Sovrascrivere i file esistenti.</span><span class="sxs-lookup"><span data-stu-id="38ec6-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="38ec6-260">Esempio:</span><span class="sxs-lookup"><span data-stu-id="38ec6-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="38ec6-261">Esempio che associa solo le tabelle selezionate e crea il contesto in una cartella separata con il nome e lo spazio dei nomi specificati:</span><span class="sxs-lookup"><span data-stu-id="38ec6-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="38ec6-262">Script-migrazione</span><span class="sxs-lookup"><span data-stu-id="38ec6-262">Script-Migration</span></span>

<span data-ttu-id="38ec6-263">Genera uno script SQL che applica tutte le modifiche a una migrazione selezionata a un'altra.</span><span class="sxs-lookup"><span data-stu-id="38ec6-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="38ec6-264">Parametri</span><span class="sxs-lookup"><span data-stu-id="38ec6-264">Parameters:</span></span>

| <span data-ttu-id="38ec6-265">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-265">Parameter</span></span>                | <span data-ttu-id="38ec6-266">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-267">*-Da* \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-267">*-From* \<String></span></span>        | <span data-ttu-id="38ec6-268">Migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="38ec6-268">The starting migration.</span></span> <span data-ttu-id="38ec6-269">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="38ec6-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="38ec6-270">Il numero 0 è un caso speciale che indica *prima della prima migrazione*.</span><span class="sxs-lookup"><span data-stu-id="38ec6-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="38ec6-271">Il valore predefinito è 0.</span><span class="sxs-lookup"><span data-stu-id="38ec6-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="38ec6-272">*-In* \<stringa></span><span class="sxs-lookup"><span data-stu-id="38ec6-272">*-To* \<String></span></span>          | <span data-ttu-id="38ec6-273">Migrazione finale.</span><span class="sxs-lookup"><span data-stu-id="38ec6-273">The ending migration.</span></span> <span data-ttu-id="38ec6-274">L'impostazione predefinita è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="38ec6-275"><nobr>-Idempotente</nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="38ec6-276">Genera uno script che può essere utilizzato in un database in qualsiasi migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="38ec6-277">-Stringa \<di output></span><span class="sxs-lookup"><span data-stu-id="38ec6-277">-Output \<String></span></span>        | <span data-ttu-id="38ec6-278">File in cui scrivere il risultato.</span><span class="sxs-lookup"><span data-stu-id="38ec6-278">The file to write the result to.</span></span> <span data-ttu-id="38ec6-279">Se questo parametro viene omesso, il file viene creato con un nome generato nella stessa cartella in cui vengono creati i file di runtime dell'applicazione, ad esempio: */obj/debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="38ec6-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="38ec6-280">I parametri to, from e output supportano la scheda-espansione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="38ec6-281">Nell'esempio seguente viene creato uno script per la migrazione di InitialCreate, usando il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="38ec6-282">Nell'esempio seguente viene creato uno script per tutte le migrazioni dopo la migrazione di InitialCreate, usando l'ID di migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="38ec6-283">Update-database</span><span class="sxs-lookup"><span data-stu-id="38ec6-283">Update-Database</span></span>

<span data-ttu-id="38ec6-284">Aggiorna il database all'ultima migrazione o a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="38ec6-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="38ec6-285">Parametro</span><span class="sxs-lookup"><span data-stu-id="38ec6-285">Parameter</span></span>                           | <span data-ttu-id="38ec6-286">Descrizione</span><span class="sxs-lookup"><span data-stu-id="38ec6-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="38ec6-287"><nobr>*->stringa di migrazione* \<</nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="38ec6-288">Migrazione di destinazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-288">The target migration.</span></span> <span data-ttu-id="38ec6-289">Le migrazioni possono essere identificate in base al nome o all'ID.</span><span class="sxs-lookup"><span data-stu-id="38ec6-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="38ec6-290">Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="38ec6-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="38ec6-291">Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="38ec6-292"><nobr>-Stringa \<di connessione></nobr></span><span class="sxs-lookup"><span data-stu-id="38ec6-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="38ec6-293">Stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="38ec6-293">The connection string to the database.</span></span> <span data-ttu-id="38ec6-294">Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="38ec6-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="38ec6-295">Il parametro Migration supporta l'espansione tramite tabulazione.</span><span class="sxs-lookup"><span data-stu-id="38ec6-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="38ec6-296">Nell'esempio seguente vengono ripristinate tutte le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="38ec6-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="38ec6-297">Gli esempi seguenti aggiornano il database a una migrazione specificata.</span><span class="sxs-lookup"><span data-stu-id="38ec6-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="38ec6-298">Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:</span><span class="sxs-lookup"><span data-stu-id="38ec6-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="38ec6-299">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="38ec6-299">Additional resources</span></span>

* [<span data-ttu-id="38ec6-300">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="38ec6-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="38ec6-301">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="38ec6-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
