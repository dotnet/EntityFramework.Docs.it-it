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
# <a name="querying-data"></a>Esecuzione di query su dati

Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database. LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate. Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database. EF Core passa una rappresentazione della query LINQ al provider di database. I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale.

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
