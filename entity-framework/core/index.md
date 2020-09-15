---
title: Panoramica di Entity Framework Core - EF Core
description: Panoramica introduttiva di Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619483"
---
# <a name="entity-framework-core"></a><span data-ttu-id="56bcb-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="56bcb-103">Entity Framework Core</span></span>

<span data-ttu-id="56bcb-104">Entity Framework (EF) Core è una versione semplice, estendibile, [open source](https://github.com/aspnet/EntityFrameworkCore) e multipiattaforma della tecnologia di accesso ai dati di grande diffusione Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="56bcb-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="56bcb-105">Entity Framework Core può essere usato come mapper relazionale a oggetti (O/RM), consentendo agli sviluppatori .NET di utilizzare un database con oggetti .NET ed eliminando la necessità della maggior parte del codice di accesso ai dati che è in genere necessario scrivere.</span><span class="sxs-lookup"><span data-stu-id="56bcb-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="56bcb-106">EF Core supporta molti motori di database. Per informazioni dettagliate, vedere [Provider di Database](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="56bcb-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="56bcb-107">Il modello</span><span class="sxs-lookup"><span data-stu-id="56bcb-107">The Model</span></span>

<span data-ttu-id="56bcb-108">Con Entity Framework Core, l'accesso ai dati viene eseguito tramite un modello.</span><span class="sxs-lookup"><span data-stu-id="56bcb-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="56bcb-109">Un modello è costituito da classi di entità e da un contesto dell'oggetto che rappresenta una sessione con il database che consente di eseguire una query e salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="56bcb-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="56bcb-110">Per altre informazioni, vedere [Creazione di un modello](xref:core/modeling/index).</span><span class="sxs-lookup"><span data-stu-id="56bcb-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="56bcb-111">È possibile generare un modello da un database esistente, scrivere manualmente il codice di un modello in modo che corrisponda al database o usare [migrazioni di Entity Framework](xref:core/managing-schemas/migrations/index) per creare un database a partire dal modello e quindi svilupparlo man mano che il modello cambia nel tempo.</span><span class="sxs-lookup"><span data-stu-id="56bcb-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="56bcb-112">Query</span><span class="sxs-lookup"><span data-stu-id="56bcb-112">Querying</span></span>

<span data-ttu-id="56bcb-113">Le istanze di classi di entità vengono recuperate dal database tramite LINQ (Language Integrated Query).</span><span class="sxs-lookup"><span data-stu-id="56bcb-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="56bcb-114">Per altre informazioni, vedere [Esecuzione di query su dati](xref:core/querying/index).</span><span class="sxs-lookup"><span data-stu-id="56bcb-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="56bcb-115">Salvataggio di dati</span><span class="sxs-lookup"><span data-stu-id="56bcb-115">Saving Data</span></span>

<span data-ttu-id="56bcb-116">I dati vengano creati, eliminati e modificati nel database tramite le istanze di classi di entità.</span><span class="sxs-lookup"><span data-stu-id="56bcb-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="56bcb-117">Per altre informazioni, vedere [Salvataggio di dati](xref:core/saving/index).</span><span class="sxs-lookup"><span data-stu-id="56bcb-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="56bcb-118">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="56bcb-118">Next steps</span></span>

<span data-ttu-id="56bcb-119">Per le esercitazioni introduttive, vedere [Introduzione a Entity Framework Core](xref:core/get-started/index).</span><span class="sxs-lookup"><span data-stu-id="56bcb-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
