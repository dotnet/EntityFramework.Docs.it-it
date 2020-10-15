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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="d9d29-103">Supporto delle tabelle Memory-Optimized in SQL Server provider di database EF Core</span><span class="sxs-lookup"><span data-stu-id="d9d29-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="d9d29-104">Le [tabelle con ottimizzazione](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) per la memoria sono una funzionalità di SQL Server in cui l'intera tabella risiede in memoria.</span><span class="sxs-lookup"><span data-stu-id="d9d29-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="d9d29-105">Una seconda copia dei dati della tabella viene mantenuta su disco, ma solo per motivi di durabilità.</span><span class="sxs-lookup"><span data-stu-id="d9d29-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="d9d29-106">I dati nelle tabelle ottimizzate per la memoria vengono letti dal disco solo durante il recupero del database.</span><span class="sxs-lookup"><span data-stu-id="d9d29-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="d9d29-107">Ad esempio dopo un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="d9d29-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="d9d29-108">Configurazione di una tabella ottimizzata per la memoria</span><span class="sxs-lookup"><span data-stu-id="d9d29-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="d9d29-109">È possibile specificare che la tabella a cui viene eseguito il mapping di un'entità è ottimizzata per la memoria.</span><span class="sxs-lookup"><span data-stu-id="d9d29-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="d9d29-110">Quando si usa EF Core per creare e gestire un database basato sul modello (con [migrazioni](xref:core/managing-schemas/migrations/index) o [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), verrà creata una tabella ottimizzata per la memoria per queste entità.</span><span class="sxs-lookup"><span data-stu-id="d9d29-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
