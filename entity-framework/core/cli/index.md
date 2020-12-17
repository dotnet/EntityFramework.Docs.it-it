---
title: Informazioni di riferimento sugli strumenti di Entity Framework Core - EF Core
description: Guida di riferimento per lo strumento dell'interfaccia della riga di comando Entity Framework Core e la console di gestione pacchetti di Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635380"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="d3782-103">Informazioni di riferimento sugli strumenti di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d3782-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="d3782-104">Gli strumenti di Entity Framework Core semplificano le attività di sviluppo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="d3782-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="d3782-105">Vengono usati principalmente per gestire le migrazioni ed eseguire lo scaffolding di un elemento `DbContext` e dei tipi di entità decompilando lo schema di un database.</span><span class="sxs-lookup"><span data-stu-id="d3782-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

<span data-ttu-id="d3782-106">È possibile installare uno degli strumenti seguenti, poiché entrambi gli strumenti espongono la stessa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="d3782-106">Either of the following tools can be installed, as both tools expose the same functionality:</span></span>

* <span data-ttu-id="d3782-107">Gli [strumenti della console di Gestione pacchetti di EF Core](xref:core/cli/powershell) vengono eseguiti nella [console di Gestione pacchetti](/nuget/tools/package-manager-console) in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d3782-107">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span> <span data-ttu-id="d3782-108">Si consiglia di usare questi strumenti se si sviluppa in Visual Studio perché forniscono un'esperienza più integrata.</span><span class="sxs-lookup"><span data-stu-id="d3782-108">We recommend using these tools if you are developing in Visual Studio as they provide a more integrated experience.</span></span>

* <span data-ttu-id="d3782-109">Gli [strumenti dell'interfaccia della riga di comando di .NET EF Core](xref:core/cli/dotnet) sono un'estensione per gli [strumenti dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="d3782-109">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="d3782-110">Tali strumenti richiedono un progetto .NET Core SDK (uno con `Sdk="Microsoft.NET.Sdk"` o simili nel file di progetto).</span><span class="sxs-lookup"><span data-stu-id="d3782-110">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d3782-111">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d3782-111">Next steps</span></span>

* [<span data-ttu-id="d3782-112">Informazioni di riferimento sugli strumenti della console di Gestione pacchetti di EF Core</span><span class="sxs-lookup"><span data-stu-id="d3782-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="d3782-113">Informazioni di riferimento sugli strumenti dell'interfaccia della riga di comando di .NET di EF Core</span><span class="sxs-lookup"><span data-stu-id="d3782-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
