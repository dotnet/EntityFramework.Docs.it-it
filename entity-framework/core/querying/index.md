---
title: Esecuzione di query su dati - EF Core
description: Panoramica delle informazioni sull'esecuzione di query in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 7cf53d59a577c5bc45f3ea4ea0cfbe257437b44e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062563"
---
# <a name="querying-data"></a><span data-ttu-id="8d3aa-103">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="8d3aa-103">Querying Data</span></span>

<span data-ttu-id="8d3aa-104">Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="8d3aa-105">LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="8d3aa-106">Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="8d3aa-107">EF Core passa una rappresentazione della query LINQ al provider di database.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="8d3aa-108">I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="8d3aa-109">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="8d3aa-110">I frammenti seguenti illustrano alcuni esempi di come eseguire attività comuni con Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8d3aa-110">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="8d3aa-111">Caricamento di tutti i dati</span><span class="sxs-lookup"><span data-stu-id="8d3aa-111">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="8d3aa-112">Caricamento di una singola entità</span><span class="sxs-lookup"><span data-stu-id="8d3aa-112">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="8d3aa-113">Filtro</span><span class="sxs-lookup"><span data-stu-id="8d3aa-113">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="8d3aa-114">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="8d3aa-114">Further readings</span></span>

- <span data-ttu-id="8d3aa-115">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="8d3aa-115">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="8d3aa-116">Per altre informazioni sulla modalità di elaborazione di una query in EF Core, vedere [Funzionamento delle query](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="8d3aa-116">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
