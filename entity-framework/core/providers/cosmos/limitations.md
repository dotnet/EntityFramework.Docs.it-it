---
title: Provider di Azure Cosmos DB-limitazioni-EF Core
description: Limitazioni del provider di Azure Cosmos DB Entity Framework Core rispetto ad altri provider
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 0b8edd9ac5fa3d684498efe222ce11a0356040b8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619283"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Limitazioni del provider di Azure Cosmos DB EF Core

Il provider Cosmos presenta alcune limitazioni. Molte di queste limitazioni sono dovute a limitazioni nel motore di database di Cosmos sottostante e non sono specifiche di EF. Ma la cosa più semplice [non è stata ancora implementata](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).

## <a name="temporary-limitations"></a>Limitazioni temporanee

- Anche se è presente un solo tipo di entità senza ereditarietà mappata a un contenitore, è ancora presente una proprietà Discriminator.
- I tipi di entità con chiavi di partizione non funzionano correttamente in alcuni scenari
- `Include` le chiamate non sono supportate
- `Join` le chiamate non sono supportate

## <a name="azure-cosmos-db-sdk-limitations"></a>Limitazioni di Azure Cosmos DB SDK

- Sono disponibili solo metodi asincroni

> [!WARNING]
> Poiché non sono presenti versioni di sincronizzazione dei metodi di basso livello EF Core si basa su, la funzionalità corrispondente è attualmente implementata chiamando `.Wait()` sull'oggetto restituito `Task` . Ciò significa che l'uso di metodi come `SaveChanges` o `ToList` anziché le relative controparti asincrone potrebbe causare un deadlock nell'applicazione

## <a name="azure-cosmos-db-limitations"></a>Limitazioni Azure Cosmos DB

È possibile visualizzare la panoramica completa delle [funzionalità supportate da Azure Cosmos DB](/azure/cosmos-db/modeling-data), che rappresentano le differenze più importanti rispetto a un database relazionale:

- Le transazioni avviate dal client non sono supportate
- Alcune query tra partizioni non sono supportate o molto più lente a seconda degli operatori interessati
