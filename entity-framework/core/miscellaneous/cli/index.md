---
title: Informazioni di riferimento sugli strumenti di Entity Framework Core - EF Core
description: Guida di riferimento per lo strumento dell'interfaccia della riga di comando Entity Framework Core e la console di gestione pacchetti di Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061996"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="b14b6-103">Informazioni di riferimento sugli strumenti di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b14b6-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="b14b6-104">Gli strumenti di Entity Framework Core semplificano le attività di sviluppo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="b14b6-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="b14b6-105">Vengono usati principalmente per gestire le migrazioni ed eseguire lo scaffolding di un elemento `DbContext` e dei tipi di entità decompilando lo schema di un database.</span><span class="sxs-lookup"><span data-stu-id="b14b6-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="b14b6-106">Gli [strumenti della console di Gestione pacchetti di EF Core](xref:core/miscellaneous/cli/powershell) vengono eseguiti nella [console di Gestione pacchetti](/nuget/tools/package-manager-console) in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b14b6-106">The [EF Core Package Manager Console tools](xref:core/miscellaneous/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="b14b6-107">Gli [strumenti dell'interfaccia della riga di comando di .NET EF Core](xref:core/miscellaneous/cli/dotnet) sono un'estensione per gli [strumenti dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="b14b6-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="b14b6-108">Tali strumenti richiedono un progetto .NET Core SDK (uno con `Sdk="Microsoft.NET.Sdk"` o simili nel file di progetto).</span><span class="sxs-lookup"><span data-stu-id="b14b6-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="b14b6-109">Entrambi gli strumenti espongono la stessa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="b14b6-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="b14b6-110">Se sviluppa in Visual Studio, è consigliabile usare gli strumenti della **console di Gestione pacchetti** poiché offrono un'esperienza più integrata.</span><span class="sxs-lookup"><span data-stu-id="b14b6-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b14b6-111">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="b14b6-111">Next steps</span></span>

* [<span data-ttu-id="b14b6-112">Informazioni di riferimento sugli strumenti della console di Gestione pacchetti di EF Core</span><span class="sxs-lookup"><span data-stu-id="b14b6-112">EF Core Package Manager Console tools reference</span></span>](xref:core/miscellaneous/cli/powershell)
* [<span data-ttu-id="b14b6-113">Informazioni di riferimento sugli strumenti dell'interfaccia della riga di comando di .NET di EF Core</span><span class="sxs-lookup"><span data-stu-id="b14b6-113">EF Core .NET CLI tools reference</span></span>](xref:core/miscellaneous/cli/dotnet)
