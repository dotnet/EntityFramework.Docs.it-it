---
title: Provider di Azure Cosmos DB-limitazioni-EF Core
description: Limitazioni del provider di Azure Cosmos DB Entity Framework Core rispetto ad altri provider
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430196"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Limitazioni del provider di Azure Cosmos DB EF Core

Il provider Cosmos presenta alcune limitazioni. Molte di queste limitazioni sono dovute a limitazioni nel motore di database di Cosmos sottostante e non sono specifiche di EF. Ma la cosa più semplice [non è stata ancora implementata](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).

## <a name="temporary-limitations"></a>Limitazioni temporanee

- `Include` le chiamate non sono supportate
- `Join` le chiamate non sono supportate

## <a name="azure-cosmos-db-sdk-limitations"></a>Limitazioni di Azure Cosmos DB SDK

- Sono disponibili solo metodi asincroni

> [!WARNING]
> Poiché non sono presenti versioni di sincronizzazione dei metodi di basso livello EF Core si basa su, la funzionalità corrispondente è attualmente implementata chiamando `.Wait()` sull'oggetto restituito `Task` . Ciò significa che l'uso di metodi come `SaveChanges` o `ToList` anziché le relative controparti asincrone potrebbe causare un deadlock nell'applicazione

## <a name="azure-cosmos-db-limitations"></a>Limitazioni Azure Cosmos DB

È possibile visualizzare la panoramica completa delle [funzionalità supportate da Azure Cosmos DB](/azure/cosmos-db/modeling-data), che rappresentano le differenze più importanti rispetto a un database relazionale:

- Le transazioni avviate dal client non sono supportate
- Alcune query tra partizioni sono più lente a seconda degli operatori interessati (ad esempio `Skip/Take` o `OFFSET LIMIT` )
