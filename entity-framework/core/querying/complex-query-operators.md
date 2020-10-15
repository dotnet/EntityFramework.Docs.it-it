---
title: Operatori di query complessi-EF Core
description: Informazioni approfondite sugli operatori di query LINQ più complessi quando si usa Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 03375e6c46a68a719df82572333f0a57e7de6262
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062620"
---
# <a name="complex-query-operators"></a>Operatori di query complessi

LINQ (Language Integrated Query) contiene molti operatori complessi, che combinano più origini dati o eseguono elaborazioni complesse. Non tutti gli operatori LINQ hanno traduzioni appropriate sul lato server. In alcuni casi, una query in un modulo viene convertita nel server, ma se scritta in un formato diverso non viene convertita anche se il risultato è lo stesso. In questa pagina vengono descritti alcuni degli operatori complessi e le relative varianti supportate. Nelle versioni future, è possibile riconoscere più modelli e aggiungere le relative traduzioni. È anche importante tenere presente che il supporto della traduzione varia tra i provider. Una query specifica, che viene convertita in SqlServer, potrebbe non funzionare per i database SQLite.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) di questo articolo in GitHub.

## <a name="join"></a>Join

L'operatore LINQ join consente di connettere due origini dati in base al selettore di chiave per ogni origine, generando una tupla di valori quando la chiave corrisponde a. Si traduce naturalmente in `INNER JOIN` database relazionali. Mentre il join LINQ dispone di selettori di chiave esterni e interni, il database richiede una singola condizione di join. Quindi EF Core genera una condizione di join confrontando il selettore di chiave esterna con il selettore di chiave interna per verificarne l'uguaglianza. Inoltre, se i selettori di chiave sono tipi anonimi, EF Core genera una condizione di join per confrontare il componente di uguaglianza Wise.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

L'operatore LINQ GroupJoin consente di connettere due origini dati simili a join, ma crea un gruppo di valori interni per la corrispondenza di elementi esterni. L'esecuzione di una query come nell'esempio seguente genera un risultato di `Blog`  &  `IEnumerable<Post>` . Poiché i database (in particolare i database relazionali) non hanno un modo per rappresentare una raccolta di oggetti lato client, GroupJoin non esegue la conversione nel server in molti casi. È necessario ottenere tutti i dati dal server per eseguire GroupJoin senza un selettore speciale (prima query riportata di seguito). Tuttavia, se il selettore limita i dati selezionati, il recupero di tutti i dati dal server può causare problemi di prestazioni (seconda query riportata di seguito). Per questo motivo EF Core non converte GroupJoin.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

L'operatore LINQ SelectMany consente di enumerare un selettore di raccolta per ogni elemento esterno e generare Tuple di valori da ogni origine dati. In un modo, si tratta di un join ma senza alcuna condizione in modo che ogni elemento esterno sia connesso a un elemento dall'origine della raccolta. A seconda del modo in cui il selettore di raccolta è correlato all'origine dati esterna, SelectMany può tradurre in diverse query sul lato server.

### <a name="collection-selector-doesnt-reference-outer"></a>Il selettore di raccolta non fa riferimento all'esterno

Quando il selettore di raccolta non fa riferimento ad alcun elemento dall'origine esterna, il risultato è un prodotto cartesiano di entrambe le origini dati. Viene convertito `CROSS JOIN` in in database relazionali.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>Riferimenti al selettore di raccolta esterni in una clausola WHERE

Quando il selettore di raccolta include una clausola WHERE, che fa riferimento all'elemento esterno, EF Core la converte in un join del database e usa il predicato come condizione di join. Questa situazione si verifica in genere quando si utilizza la navigazione della raccolta sull'elemento esterno come selettore della raccolta. Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno. Tuttavia `DefaultIfEmpty` , se viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno. A causa di questa distinzione, questo tipo di query `INNER JOIN` viene convertito in in assenza di `DefaultIfEmpty` e `LEFT JOIN` quando `DefaultIfEmpty` viene applicato.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>Riferimenti a selettori di raccolta esterni in un caso non-where

Quando il selettore della raccolta fa riferimento all'elemento esterno, che non si trova in una clausola WHERE (come nel caso precedente), non viene convertito in un join del database. Per questo motivo è necessario valutare il selettore di raccolta per ogni elemento esterno. Si traduce in `APPLY` operazioni in molti database relazionali. Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno. Tuttavia `DefaultIfEmpty` , se viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno. A causa di questa distinzione, questo tipo di query `CROSS APPLY` viene convertito in in assenza di `DefaultIfEmpty` e `OUTER APPLY` quando `DefaultIfEmpty` viene applicato. Alcuni database come SQLite non supportano `APPLY` gli operatori, quindi questo tipo di query non può essere convertito.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

Gli operatori di GroupBy LINQ creano un risultato di tipo `IGrouping<TKey, TElement>` dove `TKey` e `TElement` possono essere di qualsiasi tipo arbitrario. Inoltre, `IGrouping` implementa `IEnumerable<TElement>` , che significa che è possibile comporre su di esso utilizzando qualsiasi operatore LINQ dopo il raggruppamento. Poiché non è possibile che la struttura di database rappresenti `IGrouping` , nella maggior parte dei casi gli operatori GroupBy non hanno alcuna traduzione. Quando viene applicato un operatore di aggregazione a ogni gruppo, che restituisce un valore scalare, può essere convertito in SQL `GROUP BY` nei database relazionali. Anche SQL `GROUP BY` è restrittivo. È necessario raggruppare solo i valori scalari. La proiezione può contenere solo colonne chiave di raggruppamento o qualsiasi aggregazione applicata a una colonna. EF Core identifica questo modello e lo converte nel server, come nell'esempio seguente:

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core inoltre converte le query in cui viene visualizzato un operatore di aggregazione nel raggruppamento in un operatore LINQ WHERE o OrderBy (o un altro ordinamento). USA `HAVING` la clausola in SQL per la clausola WHERE. La parte della query prima di applicare l'operatore GroupBy può essere qualsiasi query complessa purché possa essere convertita nel server. Inoltre, dopo aver applicato gli operatori di aggregazione in una query di raggruppamento per rimuovere i raggruppamenti dall'origine risultante, è possibile comporre su di esso come qualsiasi altra query.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

Gli operatori di aggregazione EF Core supportati sono i seguenti:

- Media
- Count
- LongCount
- Max
- Min
- Sum

## <a name="left-join"></a>Left join

Mentre left join non è un operatore LINQ, i database relazionali hanno il concetto di un left join usato spesso nelle query. Un modello particolare nelle query LINQ restituisce lo stesso risultato di un oggetto nel `LEFT JOIN` Server. EF Core identifica tali modelli e genera l'equivalente `LEFT JOIN` sul lato server. Il modello prevede la creazione di un GroupJoin tra le origini dati e quindi la Flating del raggruppamento usando l'operatore SelectMany con DefaultIfEmpty nell'origine di raggruppamento in modo che corrisponda a null quando l'oggetto interno non ha un elemento correlato. Nell'esempio seguente viene illustrato l'aspetto del modello e l'elemento che genera.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

Il modello precedente crea una struttura complessa nell'albero delle espressioni. Per questo motivo, EF Core necessario rendere flat i risultati del raggruppamento dell'operatore GroupJoin in un passaggio immediatamente successivo all'operatore. Anche se GroupJoin-DefaultIfEmpty-SelectMany viene usato ma con un modello diverso, è possibile che non venga identificato come left join.
