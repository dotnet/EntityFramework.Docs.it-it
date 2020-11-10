---
title: Funzionamento delle query - EF Core
description: Informazioni generali sul modo in cui Entity Framework Core compila ed esegue internamente le query
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: 7b3014cf64f8467ccbec10598ea1bb47304dfe43
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430469"
---
# <a name="how-queries-work"></a><span data-ttu-id="18279-103">Funzionamento delle query</span><span class="sxs-lookup"><span data-stu-id="18279-103">How Queries Work</span></span>

<span data-ttu-id="18279-104">Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database.</span><span class="sxs-lookup"><span data-stu-id="18279-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="18279-105">Le query LINQ consentono di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate in base alle classi di contesto ed entità derivate.</span><span class="sxs-lookup"><span data-stu-id="18279-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

> [!NOTE]
> <span data-ttu-id="18279-106">Questo articolo è obsoleto ed è necessario aggiornarne alcune parti per tenere conto delle modifiche apportate alla progettazione della pipeline di query.</span><span class="sxs-lookup"><span data-stu-id="18279-106">This article is out of date and some parts of it needs to be updated to account for changes happened in design of query pipeline.</span></span> <span data-ttu-id="18279-107">In caso di dubbi su qualsiasi comportamento indicato, rivolgersi [a una domanda](https://github.com/dotnet/efcore/issues/new/choose).</span><span class="sxs-lookup"><span data-stu-id="18279-107">If you have any doubts about any behavior mentioned here, please [ask a question](https://github.com/dotnet/efcore/issues/new/choose).</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="18279-108">Ciclo di vita di una query</span><span class="sxs-lookup"><span data-stu-id="18279-108">The life of a query</span></span>

<span data-ttu-id="18279-109">La descrizione seguente è una panoramica di alto livello del processo di esecuzione di ogni query.</span><span class="sxs-lookup"><span data-stu-id="18279-109">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="18279-110">La query LINQ viene elaborata da Entity Framework Core per creare una rappresentazione pronta per essere elaborata dal provider di database</span><span class="sxs-lookup"><span data-stu-id="18279-110">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="18279-111">Il risultato viene memorizzato nella cache in modo che tale elaborazione non debba essere ripetuta per ogni esecuzione della query</span><span class="sxs-lookup"><span data-stu-id="18279-111">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="18279-112">Il risultato viene passato al provider di database</span><span class="sxs-lookup"><span data-stu-id="18279-112">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="18279-113">Il provider di database identifica le parti della query che possono essere valutate nel database</span><span class="sxs-lookup"><span data-stu-id="18279-113">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="18279-114">Queste parti della query vengono convertite in un linguaggio di query specifico del database, ad esempio SQL per un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="18279-114">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="18279-115">Viene inviata una query al database e il set di risultati restituito (i risultati sono valori del database, non istanze di entità)</span><span class="sxs-lookup"><span data-stu-id="18279-115">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="18279-116">Per ogni elemento nel set di risultati</span><span class="sxs-lookup"><span data-stu-id="18279-116">For each item in the result set</span></span>
   1. <span data-ttu-id="18279-117">Se la query è una query di rilevamento, EF controlla se i dati rappresentano un'entità già presente nello strumento di rilevamento delle modifiche per l'istanza del contesto</span><span class="sxs-lookup"><span data-stu-id="18279-117">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="18279-118">In caso affermativo, viene restituita l'entità esistente</span><span class="sxs-lookup"><span data-stu-id="18279-118">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="18279-119">In caso contrario, viene creata una nuova entità, il rilevamento delle modifiche viene configurato e viene restituita la nuova entità</span><span class="sxs-lookup"><span data-stu-id="18279-119">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="18279-120">Se la query è una query senza rilevamento, viene sempre creata e restituita una nuova entità</span><span class="sxs-lookup"><span data-stu-id="18279-120">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="18279-121">Quando vengono eseguite le query</span><span class="sxs-lookup"><span data-stu-id="18279-121">When queries are executed</span></span>

<span data-ttu-id="18279-122">Quando si chiamano operatori LINQ, si sta semplicemente creando una rappresentazione in memoria della query.</span><span class="sxs-lookup"><span data-stu-id="18279-122">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="18279-123">La query viene inviata al database solo al momento dell'utilizzo dei risultati.</span><span class="sxs-lookup"><span data-stu-id="18279-123">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="18279-124">Le operazioni più comuni che causano l'invio della query al database sono:</span><span class="sxs-lookup"><span data-stu-id="18279-124">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="18279-125">Iterazione dei risultati in un ciclo `for`</span><span class="sxs-lookup"><span data-stu-id="18279-125">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="18279-126">Utilizzando un operatore come `ToList` ,, `ToArray` `Single` , `Count` o gli overload asincroni equivalenti</span><span class="sxs-lookup"><span data-stu-id="18279-126">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="18279-127">**Convalidare sempre l'input dell'utente** : anche se EF Core offre protezione da attacchi SQL injection tramite l'uso di parametri e valori letterali di escape nelle query, non convalida gli input.</span><span class="sxs-lookup"><span data-stu-id="18279-127">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="18279-128">La convalida appropriata, in base ai requisiti dell'applicazione, deve essere eseguita prima che i valori provenienti da origini non attendibili vengano usati nelle query LINQ, assegnati alle proprietà dell'entità o passati ad altre API EF Core.</span><span class="sxs-lookup"><span data-stu-id="18279-128">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="18279-129">Ciò include qualsiasi input dell'utente usato per costruire query in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="18279-129">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="18279-130">Anche quando si usa LINQ, se si accetta l'input dell'utente per la creazione delle espressioni, è necessario assicurarsi che possano essere costruite solo le espressioni previste.</span><span class="sxs-lookup"><span data-stu-id="18279-130">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
