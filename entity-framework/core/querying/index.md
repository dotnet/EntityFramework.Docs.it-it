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
# <a name="querying-data"></a>Esecuzione di query su dati

Entity Framework Core utilizza Language-Integrated query (LINQ) per eseguire query sui dati dal database. LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate. Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database. EF Core passa una rappresentazione della query LINQ al provider di database. I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale. Le query vengono sempre eseguite sul database anche se le entità restituite nel risultato sono già presenti nel contesto.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) di questo articolo in GitHub.

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
