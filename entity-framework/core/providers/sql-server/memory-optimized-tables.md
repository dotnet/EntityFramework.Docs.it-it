---
title: Microsoft SQL Server provider di database-Memory-Optimized tabelle-EF Core
description: Come utilizzare Memory-Optimized tabelle con il provider di database SQL Server Entity Framework Core
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063920"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>Supporto delle tabelle Memory-Optimized in SQL Server provider di database EF Core

Le [tabelle con ottimizzazione](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) per la memoria sono una funzionalità di SQL Server in cui l'intera tabella risiede in memoria. Una seconda copia dei dati della tabella viene mantenuta su disco, ma solo per motivi di durabilità. I dati nelle tabelle ottimizzate per la memoria vengono letti dal disco solo durante il recupero del database. Ad esempio dopo un riavvio del server.

## <a name="configuring-a-memory-optimized-table"></a>Configurazione di una tabella ottimizzata per la memoria

È possibile specificare che la tabella a cui viene eseguito il mapping di un'entità è ottimizzata per la memoria. Quando si usa EF Core per creare e gestire un database basato sul modello (con [migrazioni](xref:core/managing-schemas/migrations/index) o [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), verrà creata una tabella ottimizzata per la memoria per queste entità.

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
