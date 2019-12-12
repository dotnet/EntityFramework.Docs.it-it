---
title: Provider di database Microsoft SQL Server-opzioni del database SQL di Azure-EF Core
description: Come specificare il livello di servizio e il livello di prestazioni per il database SQL di Azure con il provider di database Entity Framework Core di SQL Server
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: c4f7b91110a0e700ed06130661e611cf45bee05f
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824791"
---
# <a name="specifying-azure-sql-database-options"></a>Specifica delle opzioni del database SQL di Azure

>[!NOTE]
> Questa API è una novità di EF Core 3,1.

Il database SQL di Azure offre [un'ampia gamma di opzioni di prezzo](https://azure.microsoft.com/pricing/details/sql-database/single/) che vengono in genere configurate tramite il portale di Azure. Tuttavia, se si sta gestendo lo schema utilizzando [EF Core migrazioni](xref:core/managing-schemas/migrations/index) , è possibile specificare le opzioni desiderate nel modello stesso.

È possibile specificare il livello di servizio del database (edizione) utilizzando [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

È possibile specificare le dimensioni massime del database usando [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

È possibile specificare il livello di prestazioni del database (SERVICE_OBJECTIVE) utilizzando [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

Usare [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) per configurare il pool elastico, perché il valore non è un valore letterale stringa:

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]


>[!TIP]
> È possibile trovare tutti i valori supportati nella [documentazione di alter database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).