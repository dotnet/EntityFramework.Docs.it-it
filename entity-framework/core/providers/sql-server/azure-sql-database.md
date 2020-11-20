---
title: Provider di database Microsoft SQL Server-opzioni del database SQL di Azure-EF Core
description: Come specificare il livello di servizio e il livello di prestazioni per il database SQL di Azure con il provider di database Entity Framework Core di SQL Server
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: ad202336c2c2efdfe17776952f2a65e98222ecc0
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003588"
---
# <a name="specifying-azure-sql-database-options"></a>Impostazione delle opzioni del database SQL di Azure

>[!NOTE]
> Questa API è stata introdotta in EF Core 3,1.

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
> È possibile trovare tutti i valori supportati nella [documentazione di alter database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).
