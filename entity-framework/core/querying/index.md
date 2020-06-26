---
title: Esecuzione di query su dati - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 82f8a2e8e78e9d82f46f2fc6ced6dd9141329a22
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370340"
---
# <a name="querying-data"></a><span data-ttu-id="0bed0-102">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="0bed0-102">Querying Data</span></span>

<span data-ttu-id="0bed0-103">Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database.</span><span class="sxs-lookup"><span data-stu-id="0bed0-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="0bed0-104">LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate.</span><span class="sxs-lookup"><span data-stu-id="0bed0-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="0bed0-105">Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database.</span><span class="sxs-lookup"><span data-stu-id="0bed0-105">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="0bed0-106">EF Core passa una rappresentazione della query LINQ al provider di database.</span><span class="sxs-lookup"><span data-stu-id="0bed0-106">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="0bed0-107">I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="0bed0-107">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="0bed0-108">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="0bed0-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="0bed0-109">I frammenti seguenti illustrano alcuni esempi di come eseguire attività comuni con Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0bed0-109">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="0bed0-110">Caricamento di tutti i dati</span><span class="sxs-lookup"><span data-stu-id="0bed0-110">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="0bed0-111">Caricamento di una singola entità</span><span class="sxs-lookup"><span data-stu-id="0bed0-111">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="0bed0-112">Filtro</span><span class="sxs-lookup"><span data-stu-id="0bed0-112">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="0bed0-113">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="0bed0-113">Further readings</span></span>

- <span data-ttu-id="0bed0-114">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="0bed0-114">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="0bed0-115">Per informazioni più dettagliate sul modo in cui una query viene elaborata in EF Core, vedere funzionamento delle [query](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="0bed0-115">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
