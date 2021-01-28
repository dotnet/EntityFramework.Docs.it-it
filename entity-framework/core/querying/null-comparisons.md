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
# <a name="query-null-semantics"></a>Semantica null della query

## <a name="introduction"></a>Introduzione

I database SQL operano su una logica con 3 valori ( `true` , `false` , `null` ) quando si eseguono confronti, invece della logica booleana di C#. Durante la conversione di query LINQ in SQL, EF Core tenta di compensare la differenza introducendo ulteriori controlli null per alcuni elementi della query.
Per illustrare questo problema, è necessario definire l'entità seguente:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

ed eseguire diverse query:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

Le prime due query producono confronti semplici. Nella prima query entrambe le colonne sono non nullable, quindi i controlli null non sono necessari. Nella seconda query, `NullableInt` può contenere `null` , ma `Id` non ammette i valori `null` null; il confronto con non null restituisce `null` come risultato, che verrebbe filtrato in base all' `WHERE` operazione. Quindi, non sono necessari termini aggiuntivi.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

La terza query introduce un controllo null. Quando `NullableInt` viene `null` restituito il confronto `Id <> NullableInt` `null` , che verrebbe filtrato in base all' `WHERE` operazione. Tuttavia, dal punto di vista logico booleano, questo caso deve essere restituito come parte del risultato. Di conseguenza EF Core aggiunge il controllo necessario per assicurarsi che.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

Le query quattro e cinque mostrano il modello quando entrambe le colonne ammettono valori null. Vale la pena notare che l' `<>` operazione produce una query più complessa (e potenzialmente più lenta) rispetto all' `==` operazione.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>Trattamento dei valori nullable nelle funzioni

Molte funzioni in SQL possono restituire un `null` risultato solo se alcuni degli argomenti sono `null` . EF Core sfrutta questa funzionalità per produrre query più efficienti.
La query seguente illustra l'ottimizzazione:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

L'oggetto SQL generato è il seguente (non è necessario valutare la `SUBSTRING` funzione poiché sarà null solo quando uno degli argomenti è null):

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

L'ottimizzazione può essere usata anche per le funzioni definite dall'utente. Per ulteriori informazioni, vedere la pagina [mapping di funzioni definite dall'utente](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) .

## <a name="writing-performant-queries"></a>Scrittura di query efficienti

- Il confronto di colonne che non ammettono i valori null è più semplice e veloce rispetto al confronto tra colonne nullable. È consigliabile contrassegnare come non nullable le colonne quando possibile.

- Il controllo dell'uguaglianza ( `==` ) è più semplice e veloce rispetto alla verifica della mancata uguaglianza ( `!=` ) perché la query non deve distinguere tra `null` e il `false` risultato. Usare il confronto di uguaglianza laddove possibile. Tuttavia, la semplice negazione `==` del confronto è effettivamente uguale a `!=` , quindi non comporta un miglioramento delle prestazioni.

- In alcuni casi, è possibile semplificare un confronto complesso filtrando in `null` modo esplicito i valori di una colonna, ad esempio se non `null` sono presenti valori o se tali valori non sono rilevanti nel risultato. Si consideri l'esempio seguente:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

Queste query producono i seguenti SQL:

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

Nella seconda query, i `null` risultati vengono esclusi dalla `String1` colonna in modo esplicito. EF Core possibile considerare in modo sicuro la `String1` colonna come non nullable durante il confronto, ottenendo una query più semplice.

## <a name="using-relational-null-semantics"></a>Uso della semantica di valori null relazionali

È possibile disabilitare la compensazione del confronto null e usare direttamente la semantica null relazionale. Questa operazione può essere eseguita chiamando `UseRelationalNulls(true)` il metodo nel generatore di opzioni all'interno del `OnConfiguring` Metodo:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> Quando si usa la semantica di valori null relazionali, le query LINQ non hanno più lo stesso significato che in C# e possono produrre risultati diversi dal previsto. Prestare attenzione quando si usa questa modalità.
