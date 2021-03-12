---
title: Esecuzione di query su dati - EF Core
description: Panoramica delle informazioni sull'esecuzione di query in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 733b44e2f571bd32689b9d8e5d7507bd90e7848d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023731"
---
# <a name="querying-data"></a>Esecuzione di query su dati

Entity Framework Core utilizza Language-Integrated query (LINQ) per eseguire query sui dati dal database. LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate. Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database. EF Core passa una rappresentazione della query LINQ al provider di database. I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale. Le query vengono sempre eseguite sul database anche se le entità restituite nel risultato sono già presenti nel contesto.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Overview) di questo articolo in GitHub.

I frammenti seguenti illustrano alcuni esempi di come eseguire attività comuni con Entity Framework Core.

## <a name="loading-all-data"></a>Caricamento di tutti i dati

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>Caricamento di una singola entità

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a>Filtro

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a>Altre informazioni

- Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
- Per altre informazioni sulla modalità di elaborazione di una query in EF Core, vedere [Funzionamento delle query](xref:core/querying/how-query-works).
