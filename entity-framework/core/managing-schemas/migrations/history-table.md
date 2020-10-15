---
title: Tabella di cronologia migrazioni personalizzate-EF Core
description: Personalizzazione di una tabella di cronologia da usare per le migrazioni con Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062308"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="7c19e-103">Tabella di cronologia migrazioni personalizzate</span><span class="sxs-lookup"><span data-stu-id="7c19e-103">Custom Migrations History Table</span></span>

<span data-ttu-id="7c19e-104">Per impostazione predefinita, EF Core tiene traccia di quali migrazioni sono state applicate al database mediante la registrazione in una tabella denominata `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="7c19e-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="7c19e-105">Per diversi motivi, potrebbe essere necessario personalizzare questa tabella per adattarla alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="7c19e-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c19e-106">Se si Personalizza la tabella di cronologia delle migrazioni *dopo aver* applicato le migrazioni, si è responsabili dell'aggiornamento della tabella esistente nel database.</span><span class="sxs-lookup"><span data-stu-id="7c19e-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="7c19e-107">Nome schema e tabella</span><span class="sxs-lookup"><span data-stu-id="7c19e-107">Schema and table name</span></span>

<span data-ttu-id="7c19e-108">È possibile modificare il nome dello schema e della tabella usando il `MigrationsHistoryTable()` metodo in `OnConfiguring()` (o `ConfigureServices()` in ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="7c19e-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="7c19e-109">Di seguito è riportato un esempio che usa il provider di EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7c19e-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="7c19e-110">Altre modifiche</span><span class="sxs-lookup"><span data-stu-id="7c19e-110">Other changes</span></span>

<span data-ttu-id="7c19e-111">Per configurare altri aspetti della tabella, eseguire l'override e sostituire il servizio specifico del provider `IHistoryRepository` .</span><span class="sxs-lookup"><span data-stu-id="7c19e-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="7c19e-112">Ecco un esempio di modifica del nome della colonna MigrationId in *ID* in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7c19e-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="7c19e-113">`SqlServerHistoryRepository` si trova all'interno di uno spazio dei nomi interno e può cambiare nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="7c19e-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
