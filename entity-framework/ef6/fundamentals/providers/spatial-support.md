---
title: Supporto del provider per i tipi spaziali-EF6
description: Supporto del provider per i tipi spaziali in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: e2a543bc309001b14fb770d747a0dea22bcc91ca
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062906"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="fab0c-103">Supporto del provider per i tipi spaziali</span><span class="sxs-lookup"><span data-stu-id="fab0c-103">Provider Support for Spatial Types</span></span>
<span data-ttu-id="fab0c-104">Entity Framework supporta l'utilizzo di dati spaziali tramite le classi DbGeography o DbGeometry.</span><span class="sxs-lookup"><span data-stu-id="fab0c-104">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="fab0c-105">Queste classi si basano sulle funzionalità specifiche del database offerte dal provider Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fab0c-105">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="fab0c-106">Non tutti i provider supportano i dati spaziali e quelli che possono avere prerequisiti aggiuntivi, ad esempio l'installazione di assembly di tipo spaziale.</span><span class="sxs-lookup"><span data-stu-id="fab0c-106">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="fab0c-107">Altre informazioni sul supporto dei provider per i tipi spaziali sono disponibili di seguito.</span><span class="sxs-lookup"><span data-stu-id="fab0c-107">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="fab0c-108">Altre informazioni su come usare i tipi spaziali in un'applicazione sono disponibili in due procedure dettagliate, una per Code First, l'altra per Database First o Model First:</span><span class="sxs-lookup"><span data-stu-id="fab0c-108">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="fab0c-109">Tipi di dati spaziali in Code First</span><span class="sxs-lookup"><span data-stu-id="fab0c-109">Spatial Data Types in Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)  
- [<span data-ttu-id="fab0c-110">Tipi di dati spaziali in EF designer</span><span class="sxs-lookup"><span data-stu-id="fab0c-110">Spatial Data Types in EF Designer</span></span>](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="fab0c-111">Versioni EF che supportano i tipi spaziali</span><span class="sxs-lookup"><span data-stu-id="fab0c-111">EF releases that support spatial types</span></span>  

<span data-ttu-id="fab0c-112">Il supporto per i tipi spaziali è stato introdotto in EF5.</span><span class="sxs-lookup"><span data-stu-id="fab0c-112">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="fab0c-113">Tuttavia, nei tipi spaziali EF5 sono supportati solo quando l'applicazione è destinata ed è in esecuzione in .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="fab0c-113">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="fab0c-114">A partire da i tipi spaziali EF6 sono supportati per le applicazioni destinate sia a .NET 4 che a .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="fab0c-114">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="fab0c-115">Provider EF che supportano i tipi spaziali</span><span class="sxs-lookup"><span data-stu-id="fab0c-115">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="fab0c-116">EF5</span><span class="sxs-lookup"><span data-stu-id="fab0c-116">EF5</span></span>  

<span data-ttu-id="fab0c-117">I provider Entity Framework per EF5 che sono in grado di supportare i tipi spaziali sono:</span><span class="sxs-lookup"><span data-stu-id="fab0c-117">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="fab0c-118">Provider Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="fab0c-118">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="fab0c-119">Questo provider viene fornito come parte di EF5.</span><span class="sxs-lookup"><span data-stu-id="fab0c-119">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="fab0c-120">Questo provider dipende da alcune librerie di basso livello aggiuntive che potrebbero dover essere installate. per informazioni dettagliate, vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="fab0c-120">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="fab0c-121">Devart dotConnect per Oracle</span><span class="sxs-lookup"><span data-stu-id="fab0c-121">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="fab0c-122">Si tratta di un provider di terze parti di Devart.</span><span class="sxs-lookup"><span data-stu-id="fab0c-122">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="fab0c-123">Se si è a conoscenza di un provider EF5 che supporta i tipi spaziali, contattare il contatto e sarà possibile aggiungerlo a questo elenco.</span><span class="sxs-lookup"><span data-stu-id="fab0c-123">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="fab0c-124">EF6</span><span class="sxs-lookup"><span data-stu-id="fab0c-124">EF6</span></span>  

<span data-ttu-id="fab0c-125">I provider Entity Framework per EF6 che sono in grado di supportare i tipi spaziali sono:</span><span class="sxs-lookup"><span data-stu-id="fab0c-125">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="fab0c-126">Provider Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="fab0c-126">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="fab0c-127">Questo provider viene fornito come parte di EF6.</span><span class="sxs-lookup"><span data-stu-id="fab0c-127">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="fab0c-128">Questo provider dipende da alcune librerie di basso livello aggiuntive che potrebbero dover essere installate. per informazioni dettagliate, vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="fab0c-128">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="fab0c-129">Devart dotConnect per Oracle</span><span class="sxs-lookup"><span data-stu-id="fab0c-129">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="fab0c-130">Si tratta di un provider di terze parti di Devart.</span><span class="sxs-lookup"><span data-stu-id="fab0c-130">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="fab0c-131">Se si è a conoscenza di un provider EF6 che supporta i tipi spaziali, contattare il contatto e sarà possibile aggiungerlo a questo elenco.</span><span class="sxs-lookup"><span data-stu-id="fab0c-131">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="fab0c-132">Prerequisiti per i tipi spaziali con Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="fab0c-132">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="fab0c-133">SQL Server supporto spaziale dipende dai tipi SqlGeography e SqlGeometry di basso livello SQL Server specifici.</span><span class="sxs-lookup"><span data-stu-id="fab0c-133">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="fab0c-134">Questi tipi risiedono in Microsoft.SqlServer.Types.dll assembly e questo assembly non viene fornito come parte di EF o come parte del .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="fab0c-134">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="fab0c-135">Quando si installa Visual Studio, viene spesso installata anche una versione di SQL Server, che includerà l'installazione del Microsoft.SqlServer.Types.dll.</span><span class="sxs-lookup"><span data-stu-id="fab0c-135">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="fab0c-136">Se SQL Server non è installato nel computer in cui si desidera utilizzare tipi spaziali o se i tipi spaziali sono stati esclusi dall'installazione SQL Server, sarà necessario installarli manualmente.</span><span class="sxs-lookup"><span data-stu-id="fab0c-136">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="fab0c-137">I tipi possono essere installati utilizzando `SQLSysClrTypes.msi` , che fa parte di Microsoft SQL Server Feature Pack.</span><span class="sxs-lookup"><span data-stu-id="fab0c-137">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="fab0c-138">I tipi spaziali sono SQL Server specifici della versione, quindi è consigliabile [cercare "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) nell'area download Microsoft, quindi selezionare e scaricare l'opzione che corrisponde alla versione di SQL Server che verrà usata.</span><span class="sxs-lookup"><span data-stu-id="fab0c-138">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
