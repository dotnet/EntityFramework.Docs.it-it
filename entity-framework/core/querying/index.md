---
title: Esecuzione di query su dati - EF Core
description: Panoramica delle informazioni sull'esecuzione di query in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430118"
---
# <a name="querying-data"></a><span data-ttu-id="e272e-103">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="e272e-103">Querying Data</span></span>

<span data-ttu-id="e272e-104">Entity Framework Core utilizza Language-Integrated query (LINQ) per eseguire query sui dati dal database.</span><span class="sxs-lookup"><span data-stu-id="e272e-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="e272e-105">LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate.</span><span class="sxs-lookup"><span data-stu-id="e272e-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="e272e-106">Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database.</span><span class="sxs-lookup"><span data-stu-id="e272e-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="e272e-107">EF Core passa una rappresentazione della query LINQ al provider di database.</span><span class="sxs-lookup"><span data-stu-id="e272e-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="e272e-108">I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="e272e-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="e272e-109">Le query vengono sempre eseguite sul database anche se le entità restituite nel risultato sono già presenti nel contesto.</span><span class="sxs-lookup"><span data-stu-id="e272e-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="e272e-110">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="e272e-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="e272e-111">I frammenti seguenti illustrano alcuni esempi di come eseguire attività comuni con Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e272e-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="e272e-112">Caricamento di tutti i dati</span><span class="sxs-lookup"><span data-stu-id="e272e-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="e272e-113">Caricamento di una singola entità</span><span class="sxs-lookup"><span data-stu-id="e272e-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="e272e-114">Filtro</span><span class="sxs-lookup"><span data-stu-id="e272e-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="e272e-115">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e272e-115">Further readings</span></span>

- <span data-ttu-id="e272e-116">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="e272e-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="e272e-117">Per altre informazioni sulla modalità di elaborazione di una query in EF Core, vedere [Funzionamento delle query](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="e272e-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
