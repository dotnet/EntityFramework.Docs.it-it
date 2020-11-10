---
title: Servizi in fase di progettazione-EF Core
description: Informazioni su Entity Framework Core servizi in fase di progettazione
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431571"
---
# <a name="design-time-services"></a><span data-ttu-id="2bd61-103">Servizi in fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="2bd61-103">Design-time services</span></span>

<span data-ttu-id="2bd61-104">Alcuni servizi utilizzati dagli strumenti vengono utilizzati solo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="2bd61-105">Questi servizi vengono gestiti separatamente dai servizi di runtime di EF Core per impedire che vengano distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="2bd61-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="2bd61-106">Per eseguire l'override di uno di questi servizi, ad esempio il servizio per generare file di migrazione, aggiungere un'implementazione di `IDesignTimeServices` al progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="2bd61-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="2bd61-107">Riferimento a Microsoft. EntityFrameworkCore. Design</span><span class="sxs-lookup"><span data-stu-id="2bd61-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="2bd61-108">Microsoft. EntityFrameworkCore. Design è un pacchetto DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="2bd61-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="2bd61-109">Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che, per impostazione predefinita, non è possibile fare riferimento ai relativi tipi.</span><span class="sxs-lookup"><span data-stu-id="2bd61-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="2bd61-110">Per fare riferimento ai relativi tipi ed eseguire l'override dei servizi in fase di progettazione, aggiornare i metadati dell'elemento PackageReference nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="2bd61-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="2bd61-111">Se al pacchetto viene fatto riferimento in modo transitivo tramite Microsoft. EntityFrameworkCore. Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto e modificarne i metadati.</span><span class="sxs-lookup"><span data-stu-id="2bd61-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="2bd61-112">Elenco dei servizi</span><span class="sxs-lookup"><span data-stu-id="2bd61-112">List of services</span></span>

<span data-ttu-id="2bd61-113">Di seguito è riportato un elenco dei servizi in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="2bd61-114">Servizio</span><span class="sxs-lookup"><span data-stu-id="2bd61-114">Service</span></span>                                                                              | <span data-ttu-id="2bd61-115">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2bd61-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="2bd61-116">Genera il codice per le annotazioni del modello corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="2bd61-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="2bd61-117">Consente di generare il codice C#.</span><span class="sxs-lookup"><span data-stu-id="2bd61-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="2bd61-118">Parole plurali e singolari.</span><span class="sxs-lookup"><span data-stu-id="2bd61-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="2bd61-119">Genera il codice per una migrazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="2bd61-120">Classe principale per la gestione dei file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="2bd61-121">Consente di creare un modello di database da un database.</span><span class="sxs-lookup"><span data-stu-id="2bd61-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="2bd61-122">Genera il codice per un modello.</span><span class="sxs-lookup"><span data-stu-id="2bd61-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="2bd61-123">Genera il codice di onconfigurazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="2bd61-124">Classe principale per l'impalcatura di modelli decodificati.</span><span class="sxs-lookup"><span data-stu-id="2bd61-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="2bd61-125">Uso dei servizi</span><span class="sxs-lookup"><span data-stu-id="2bd61-125">Using services</span></span>

<span data-ttu-id="2bd61-126">Questi servizi possono essere utili anche per creare strumenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="2bd61-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="2bd61-127">Ad esempio, quando si vuole automatizzare parte del flusso di lavoro della fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="2bd61-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="2bd61-128">È possibile compilare un provider di servizi che contiene questi servizi usando i metodi di estensione AddEntityFrameworkDesignTimeServices e AddDbContextDesignTimeServices.</span><span class="sxs-lookup"><span data-stu-id="2bd61-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
