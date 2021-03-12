---
title: Operatori di query complessi-EF Core
description: Informazioni approfondite sugli operatori di query LINQ più complessi quando si usa Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: bdcb2f4d00ee4833ae9e0a10d307e5124bafb4b6
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023757"
---
# <a name="complex-query-operators"></a><span data-ttu-id="0e11b-103">Operatori di query complessi</span><span class="sxs-lookup"><span data-stu-id="0e11b-103">Complex Query Operators</span></span>

<span data-ttu-id="0e11b-104">LINQ (Language Integrated Query) contiene molti operatori complessi, che combinano più origini dati o eseguono elaborazioni complesse.</span><span class="sxs-lookup"><span data-stu-id="0e11b-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="0e11b-105">Non tutti gli operatori LINQ hanno traduzioni appropriate sul lato server.</span><span class="sxs-lookup"><span data-stu-id="0e11b-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="0e11b-106">In alcuni casi, una query in un modulo viene convertita nel server, ma se scritta in un formato diverso non viene convertita anche se il risultato è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="0e11b-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="0e11b-107">In questa pagina vengono descritti alcuni degli operatori complessi e le relative varianti supportate.</span><span class="sxs-lookup"><span data-stu-id="0e11b-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="0e11b-108">Nelle versioni future, è possibile riconoscere più modelli e aggiungere le relative traduzioni.</span><span class="sxs-lookup"><span data-stu-id="0e11b-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="0e11b-109">È anche importante tenere presente che il supporto della traduzione varia tra i provider.</span><span class="sxs-lookup"><span data-stu-id="0e11b-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="0e11b-110">Una query specifica, che viene convertita in SqlServer, potrebbe non funzionare per i database SQLite.</span><span class="sxs-lookup"><span data-stu-id="0e11b-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="0e11b-111">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/ComplexQuery) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="0e11b-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/ComplexQuery) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="0e11b-112">Join</span><span class="sxs-lookup"><span data-stu-id="0e11b-112">Join</span></span>

<span data-ttu-id="0e11b-113">L'operatore LINQ join consente di connettere due origini dati in base al selettore di chiave per ogni origine, generando una tupla di valori quando la chiave corrisponde a.</span><span class="sxs-lookup"><span data-stu-id="0e11b-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="0e11b-114">Si traduce naturalmente in `INNER JOIN` database relazionali.</span><span class="sxs-lookup"><span data-stu-id="0e11b-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="0e11b-115">Mentre il join LINQ dispone di selettori di chiave esterni e interni, il database richiede una singola condizione di join.</span><span class="sxs-lookup"><span data-stu-id="0e11b-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="0e11b-116">Quindi EF Core genera una condizione di join confrontando il selettore di chiave esterna con il selettore di chiave interna per verificarne l'uguaglianza.</span><span class="sxs-lookup"><span data-stu-id="0e11b-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

<span data-ttu-id="0e11b-117">Inoltre, se i selettori di chiave sono tipi anonimi, EF Core genera una condizione di join per confrontare l'uguaglianza con i componenti.</span><span class="sxs-lookup"><span data-stu-id="0e11b-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component-wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#JoinComposite)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON ([p0].[PersonPhotoId] = [p].[PhotoId] AND ([p0].[Caption] = N'SN'))
```

## <a name="groupjoin"></a><span data-ttu-id="0e11b-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="0e11b-118">GroupJoin</span></span>

<span data-ttu-id="0e11b-119">L'operatore LINQ GroupJoin consente di connettere due origini dati simili a join, ma crea un gruppo di valori interni per la corrispondenza di elementi esterni.</span><span class="sxs-lookup"><span data-stu-id="0e11b-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="0e11b-120">L'esecuzione di una query come nell'esempio seguente genera un risultato di `Blog`  &  `IEnumerable<Post>` .</span><span class="sxs-lookup"><span data-stu-id="0e11b-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="0e11b-121">Poiché i database (in particolare i database relazionali) non hanno un modo per rappresentare una raccolta di oggetti lato client, GroupJoin non esegue la conversione nel server in molti casi.</span><span class="sxs-lookup"><span data-stu-id="0e11b-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="0e11b-122">È necessario ottenere tutti i dati dal server per eseguire GroupJoin senza un selettore speciale (prima query riportata di seguito).</span><span class="sxs-lookup"><span data-stu-id="0e11b-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="0e11b-123">Tuttavia, se il selettore limita i dati selezionati, il recupero di tutti i dati dal server può causare problemi di prestazioni (seconda query riportata di seguito).</span><span class="sxs-lookup"><span data-stu-id="0e11b-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="0e11b-124">Per questo motivo EF Core non converte GroupJoin.</span><span class="sxs-lookup"><span data-stu-id="0e11b-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="0e11b-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="0e11b-125">SelectMany</span></span>

<span data-ttu-id="0e11b-126">L'operatore LINQ SelectMany consente di enumerare un selettore di raccolta per ogni elemento esterno e generare Tuple di valori da ogni origine dati.</span><span class="sxs-lookup"><span data-stu-id="0e11b-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="0e11b-127">In un modo, si tratta di un join ma senza alcuna condizione in modo che ogni elemento esterno sia connesso a un elemento dall'origine della raccolta.</span><span class="sxs-lookup"><span data-stu-id="0e11b-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="0e11b-128">A seconda del modo in cui il selettore di raccolta è correlato all'origine dati esterna, SelectMany può tradurre in diverse query sul lato server.</span><span class="sxs-lookup"><span data-stu-id="0e11b-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="0e11b-129">Il selettore di raccolta non fa riferimento all'esterno</span><span class="sxs-lookup"><span data-stu-id="0e11b-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="0e11b-130">Quando il selettore di raccolta non fa riferimento ad alcun elemento dall'origine esterna, il risultato è un prodotto cartesiano di entrambe le origini dati.</span><span class="sxs-lookup"><span data-stu-id="0e11b-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="0e11b-131">Viene convertito `CROSS JOIN` in in database relazionali.</span><span class="sxs-lookup"><span data-stu-id="0e11b-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="0e11b-132">Riferimenti al selettore di raccolta esterni in una clausola WHERE</span><span class="sxs-lookup"><span data-stu-id="0e11b-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="0e11b-133">Quando il selettore di raccolta include una clausola WHERE, che fa riferimento all'elemento esterno, EF Core la converte in un join del database e usa il predicato come condizione di join.</span><span class="sxs-lookup"><span data-stu-id="0e11b-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="0e11b-134">Questa situazione si verifica in genere quando si utilizza la navigazione della raccolta sull'elemento esterno come selettore della raccolta.</span><span class="sxs-lookup"><span data-stu-id="0e11b-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="0e11b-135">Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="0e11b-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="0e11b-136">Tuttavia `DefaultIfEmpty` , se viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno.</span><span class="sxs-lookup"><span data-stu-id="0e11b-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="0e11b-137">A causa di questa distinzione, questo tipo di query `INNER JOIN` viene convertito in in assenza di `DefaultIfEmpty` e `LEFT JOIN` quando `DefaultIfEmpty` viene applicato.</span><span class="sxs-lookup"><span data-stu-id="0e11b-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="0e11b-138">Riferimenti a selettori di raccolta esterni in un caso non-where</span><span class="sxs-lookup"><span data-stu-id="0e11b-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="0e11b-139">Quando il selettore della raccolta fa riferimento all'elemento esterno, che non si trova in una clausola WHERE (come nel caso precedente), non viene convertito in un join del database.</span><span class="sxs-lookup"><span data-stu-id="0e11b-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="0e11b-140">Per questo motivo è necessario valutare il selettore di raccolta per ogni elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="0e11b-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="0e11b-141">Si traduce in `APPLY` operazioni in molti database relazionali.</span><span class="sxs-lookup"><span data-stu-id="0e11b-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="0e11b-142">Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="0e11b-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="0e11b-143">Tuttavia `DefaultIfEmpty` , se viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno.</span><span class="sxs-lookup"><span data-stu-id="0e11b-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="0e11b-144">A causa di questa distinzione, questo tipo di query `CROSS APPLY` viene convertito in in assenza di `DefaultIfEmpty` e `OUTER APPLY` quando `DefaultIfEmpty` viene applicato.</span><span class="sxs-lookup"><span data-stu-id="0e11b-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="0e11b-145">Alcuni database come SQLite non supportano `APPLY` gli operatori, quindi questo tipo di query non può essere convertito.</span><span class="sxs-lookup"><span data-stu-id="0e11b-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="0e11b-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="0e11b-146">GroupBy</span></span>

<span data-ttu-id="0e11b-147">Gli operatori di GroupBy LINQ creano un risultato di tipo `IGrouping<TKey, TElement>` dove `TKey` e `TElement` possono essere di qualsiasi tipo arbitrario.</span><span class="sxs-lookup"><span data-stu-id="0e11b-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="0e11b-148">Inoltre, `IGrouping` implementa `IEnumerable<TElement>` , che significa che è possibile comporre su di esso utilizzando qualsiasi operatore LINQ dopo il raggruppamento.</span><span class="sxs-lookup"><span data-stu-id="0e11b-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="0e11b-149">Poiché non è possibile che la struttura di database rappresenti `IGrouping` , nella maggior parte dei casi gli operatori GroupBy non hanno alcuna traduzione.</span><span class="sxs-lookup"><span data-stu-id="0e11b-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="0e11b-150">Quando viene applicato un operatore di aggregazione a ogni gruppo, che restituisce un valore scalare, può essere convertito in SQL `GROUP BY` nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="0e11b-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="0e11b-151">Anche SQL `GROUP BY` è restrittivo.</span><span class="sxs-lookup"><span data-stu-id="0e11b-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="0e11b-152">È necessario raggruppare solo i valori scalari.</span><span class="sxs-lookup"><span data-stu-id="0e11b-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="0e11b-153">La proiezione può contenere solo colonne chiave di raggruppamento o qualsiasi aggregazione applicata a una colonna.</span><span class="sxs-lookup"><span data-stu-id="0e11b-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="0e11b-154">EF Core identifica questo modello e lo converte nel server, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0e11b-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="0e11b-155">EF Core inoltre converte le query in cui viene visualizzato un operatore di aggregazione nel raggruppamento in un operatore LINQ WHERE o OrderBy (o un altro ordinamento).</span><span class="sxs-lookup"><span data-stu-id="0e11b-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="0e11b-156">USA `HAVING` la clausola in SQL per la clausola WHERE.</span><span class="sxs-lookup"><span data-stu-id="0e11b-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="0e11b-157">La parte della query prima di applicare l'operatore GroupBy può essere qualsiasi query complessa purché possa essere convertita nel server.</span><span class="sxs-lookup"><span data-stu-id="0e11b-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="0e11b-158">Inoltre, dopo aver applicato gli operatori di aggregazione in una query di raggruppamento per rimuovere i raggruppamenti dall'origine risultante, è possibile comporre su di esso come qualsiasi altra query.</span><span class="sxs-lookup"><span data-stu-id="0e11b-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="0e11b-159">Gli operatori di aggregazione EF Core supportati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="0e11b-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="0e11b-160">Media</span><span class="sxs-lookup"><span data-stu-id="0e11b-160">Average</span></span>
- <span data-ttu-id="0e11b-161">Conteggio</span><span class="sxs-lookup"><span data-stu-id="0e11b-161">Count</span></span>
- <span data-ttu-id="0e11b-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="0e11b-162">LongCount</span></span>
- <span data-ttu-id="0e11b-163">Max</span><span class="sxs-lookup"><span data-stu-id="0e11b-163">Max</span></span>
- <span data-ttu-id="0e11b-164">Min</span><span class="sxs-lookup"><span data-stu-id="0e11b-164">Min</span></span>
- <span data-ttu-id="0e11b-165">Sum</span><span class="sxs-lookup"><span data-stu-id="0e11b-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="0e11b-166">Left join</span><span class="sxs-lookup"><span data-stu-id="0e11b-166">Left Join</span></span>

<span data-ttu-id="0e11b-167">Mentre left join non è un operatore LINQ, i database relazionali hanno il concetto di un left join usato spesso nelle query.</span><span class="sxs-lookup"><span data-stu-id="0e11b-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="0e11b-168">Un modello particolare nelle query LINQ restituisce lo stesso risultato di un oggetto nel `LEFT JOIN` Server.</span><span class="sxs-lookup"><span data-stu-id="0e11b-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="0e11b-169">EF Core identifica tali modelli e genera l'equivalente `LEFT JOIN` sul lato server.</span><span class="sxs-lookup"><span data-stu-id="0e11b-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="0e11b-170">Il modello prevede la creazione di un GroupJoin tra le origini dati e quindi la Flating del raggruppamento usando l'operatore SelectMany con DefaultIfEmpty nell'origine di raggruppamento in modo che corrisponda a null quando l'oggetto interno non ha un elemento correlato.</span><span class="sxs-lookup"><span data-stu-id="0e11b-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="0e11b-171">Nell'esempio seguente viene illustrato l'aspetto del modello e l'elemento che genera.</span><span class="sxs-lookup"><span data-stu-id="0e11b-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="0e11b-172">Il modello precedente crea una struttura complessa nell'albero delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="0e11b-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="0e11b-173">Per questo motivo, EF Core necessario rendere flat i risultati del raggruppamento dell'operatore GroupJoin in un passaggio immediatamente successivo all'operatore.</span><span class="sxs-lookup"><span data-stu-id="0e11b-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="0e11b-174">Anche se GroupJoin-DefaultIfEmpty-SelectMany viene usato ma con un modello diverso, è possibile che non venga identificato come left join.</span><span class="sxs-lookup"><span data-stu-id="0e11b-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
