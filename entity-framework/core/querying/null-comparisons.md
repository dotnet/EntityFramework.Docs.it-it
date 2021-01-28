---
title: Confronti con valori null nelle query
description: Informazioni sul modo in cui Entity Framework Core gestisce i confronti NULL nelle query
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983352"
---
# <a name="query-null-semantics"></a><span data-ttu-id="4cb45-103">Semantica null della query</span><span class="sxs-lookup"><span data-stu-id="4cb45-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="4cb45-104">Introduzione</span><span class="sxs-lookup"><span data-stu-id="4cb45-104">Introduction</span></span>

<span data-ttu-id="4cb45-105">I database SQL operano su una logica con 3 valori ( `true` , `false` , `null` ) quando si eseguono confronti, invece della logica booleana di C#.</span><span class="sxs-lookup"><span data-stu-id="4cb45-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="4cb45-106">Durante la conversione di query LINQ in SQL, EF Core tenta di compensare la differenza introducendo ulteriori controlli null per alcuni elementi della query.</span><span class="sxs-lookup"><span data-stu-id="4cb45-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="4cb45-107">Per illustrare questo problema, è necessario definire l'entità seguente:</span><span class="sxs-lookup"><span data-stu-id="4cb45-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="4cb45-108">ed eseguire diverse query:</span><span class="sxs-lookup"><span data-stu-id="4cb45-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="4cb45-109">Le prime due query producono confronti semplici.</span><span class="sxs-lookup"><span data-stu-id="4cb45-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="4cb45-110">Nella prima query entrambe le colonne sono non nullable, quindi i controlli null non sono necessari.</span><span class="sxs-lookup"><span data-stu-id="4cb45-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="4cb45-111">Nella seconda query, `NullableInt` può contenere `null` , ma `Id` non ammette i valori `null` null; il confronto con non null restituisce `null` come risultato, che verrebbe filtrato in base all' `WHERE` operazione.</span><span class="sxs-lookup"><span data-stu-id="4cb45-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="4cb45-112">Quindi, non sono necessari termini aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="4cb45-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="4cb45-113">La terza query introduce un controllo null.</span><span class="sxs-lookup"><span data-stu-id="4cb45-113">The third query introduces a null check.</span></span> <span data-ttu-id="4cb45-114">Quando `NullableInt` viene `null` restituito il confronto `Id <> NullableInt` `null` , che verrebbe filtrato in base all' `WHERE` operazione.</span><span class="sxs-lookup"><span data-stu-id="4cb45-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="4cb45-115">Tuttavia, dal punto di vista logico booleano, questo caso deve essere restituito come parte del risultato.</span><span class="sxs-lookup"><span data-stu-id="4cb45-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="4cb45-116">Di conseguenza EF Core aggiunge il controllo necessario per assicurarsi che.</span><span class="sxs-lookup"><span data-stu-id="4cb45-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="4cb45-117">Le query quattro e cinque mostrano il modello quando entrambe le colonne ammettono valori null.</span><span class="sxs-lookup"><span data-stu-id="4cb45-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="4cb45-118">Vale la pena notare che l' `<>` operazione produce una query più complessa (e potenzialmente più lenta) rispetto all' `==` operazione.</span><span class="sxs-lookup"><span data-stu-id="4cb45-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="4cb45-119">Trattamento dei valori nullable nelle funzioni</span><span class="sxs-lookup"><span data-stu-id="4cb45-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="4cb45-120">Molte funzioni in SQL possono restituire un `null` risultato solo se alcuni degli argomenti sono `null` .</span><span class="sxs-lookup"><span data-stu-id="4cb45-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="4cb45-121">EF Core sfrutta questa funzionalità per produrre query più efficienti.</span><span class="sxs-lookup"><span data-stu-id="4cb45-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="4cb45-122">La query seguente illustra l'ottimizzazione:</span><span class="sxs-lookup"><span data-stu-id="4cb45-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="4cb45-123">L'oggetto SQL generato è il seguente (non è necessario valutare la `SUBSTRING` funzione poiché sarà null solo quando uno degli argomenti è null):</span><span class="sxs-lookup"><span data-stu-id="4cb45-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="4cb45-124">L'ottimizzazione può essere usata anche per le funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="4cb45-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="4cb45-125">Per ulteriori informazioni, vedere la pagina [mapping di funzioni definite dall'utente](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) .</span><span class="sxs-lookup"><span data-stu-id="4cb45-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="4cb45-126">Scrittura di query efficienti</span><span class="sxs-lookup"><span data-stu-id="4cb45-126">Writing performant queries</span></span>

- <span data-ttu-id="4cb45-127">Il confronto di colonne che non ammettono i valori null è più semplice e veloce rispetto al confronto tra colonne nullable.</span><span class="sxs-lookup"><span data-stu-id="4cb45-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="4cb45-128">È consigliabile contrassegnare come non nullable le colonne quando possibile.</span><span class="sxs-lookup"><span data-stu-id="4cb45-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="4cb45-129">Il controllo dell'uguaglianza ( `==` ) è più semplice e veloce rispetto alla verifica della mancata uguaglianza ( `!=` ) perché la query non deve distinguere tra `null` e il `false` risultato.</span><span class="sxs-lookup"><span data-stu-id="4cb45-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="4cb45-130">Usare il confronto di uguaglianza laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="4cb45-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="4cb45-131">Tuttavia, la semplice negazione `==` del confronto è effettivamente uguale a `!=` , quindi non comporta un miglioramento delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4cb45-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="4cb45-132">In alcuni casi, è possibile semplificare un confronto complesso filtrando in `null` modo esplicito i valori di una colonna, ad esempio se non `null` sono presenti valori o se tali valori non sono rilevanti nel risultato.</span><span class="sxs-lookup"><span data-stu-id="4cb45-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="4cb45-133">Si consideri l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4cb45-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="4cb45-134">Queste query producono i seguenti SQL:</span><span class="sxs-lookup"><span data-stu-id="4cb45-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="4cb45-135">Nella seconda query, i `null` risultati vengono esclusi dalla `String1` colonna in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4cb45-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="4cb45-136">EF Core possibile considerare in modo sicuro la `String1` colonna come non nullable durante il confronto, ottenendo una query più semplice.</span><span class="sxs-lookup"><span data-stu-id="4cb45-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="4cb45-137">Uso della semantica di valori null relazionali</span><span class="sxs-lookup"><span data-stu-id="4cb45-137">Using relational null semantics</span></span>

<span data-ttu-id="4cb45-138">È possibile disabilitare la compensazione del confronto null e usare direttamente la semantica null relazionale.</span><span class="sxs-lookup"><span data-stu-id="4cb45-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="4cb45-139">Questa operazione può essere eseguita chiamando `UseRelationalNulls(true)` il metodo nel generatore di opzioni all'interno del `OnConfiguring` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4cb45-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="4cb45-140">Quando si usa la semantica di valori null relazionali, le query LINQ non hanno più lo stesso significato che in C# e possono produrre risultati diversi dal previsto.</span><span class="sxs-lookup"><span data-stu-id="4cb45-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="4cb45-141">Prestare attenzione quando si usa questa modalità.</span><span class="sxs-lookup"><span data-stu-id="4cb45-141">Exercise caution when using this mode.</span></span>
