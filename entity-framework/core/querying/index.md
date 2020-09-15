---
title: Esecuzione di query su dati - EF Core
description: Panoramica delle informazioni sull'esecuzione di query in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 028c640a17c4946158c86bdf1a663a4050f55921
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616331"
---
# <a name="querying-data"></a>Esecuzione di query su dati

Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database. LINQ consente di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate. Usa le classi di contesto ed entità derivate per fare riferimento agli oggetti di database. EF Core passa una rappresentazione della query LINQ al provider di database. I provider di database a loro volta la convertono nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.

I frammenti seguenti illustrano alcuni esempi di come eseguire attività comuni con Entity Framework Core.

## <a name="loading-all-data"></a>Caricamento di tutti i dati

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>Caricamento di una singola entità

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>Filtro

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>Altre informazioni

- Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
- Per altre informazioni sulla modalità di elaborazione di una query in EF Core, vedere [Funzionamento delle query](xref:core/querying/how-query-works).
