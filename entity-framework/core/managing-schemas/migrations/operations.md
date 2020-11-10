---
title: Operazioni di migrazione personalizzate-EF Core
description: Gestione di migrazioni SQL personalizzate e non elaborate per la gestione dello schema del database con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429832"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="5749a-103">Operazioni di migrazione personalizzate</span><span class="sxs-lookup"><span data-stu-id="5749a-103">Custom Migrations Operations</span></span>

<span data-ttu-id="5749a-104">L'API MigrationBuilder consente di eseguire molti tipi diversi di operazioni durante una migrazione, ma è molto più esaustiva.</span><span class="sxs-lookup"><span data-stu-id="5749a-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="5749a-105">Tuttavia, l'API è anche estendibile e consente di definire le proprie operazioni.</span><span class="sxs-lookup"><span data-stu-id="5749a-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="5749a-106">Esistono due modi per estendere l'API: usando il `Sql()` metodo o definendo `MigrationOperation` oggetti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="5749a-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="5749a-107">Per illustrare l'implementazione di un'operazione che consente di creare un utente del database utilizzando ogni approccio.</span><span class="sxs-lookup"><span data-stu-id="5749a-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="5749a-108">Nelle migrazioni è necessario abilitare la scrittura del codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5749a-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="5749a-109">Utilizzo di MigrationBuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="5749a-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="5749a-110">Il modo più semplice per implementare un'operazione personalizzata consiste nel definire un metodo di estensione che chiama `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="5749a-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="5749a-111">Di seguito è riportato un esempio che genera l'oggetto Transact-SQL appropriato.</span><span class="sxs-lookup"><span data-stu-id="5749a-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="5749a-112">Utilizzare la `EXEC` funzione quando un'istruzione deve essere la prima o una sola in un batch SQL.</span><span class="sxs-lookup"><span data-stu-id="5749a-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="5749a-113">Potrebbe anche essere necessario aggirare gli errori del parser negli script di migrazione idempotente che possono verificarsi quando le colonne a cui si fa riferimento non sono attualmente presenti in una tabella.</span><span class="sxs-lookup"><span data-stu-id="5749a-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="5749a-114">Se le migrazioni devono supportare più provider di database, è possibile usare la `MigrationBuilder.ActiveProvider` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="5749a-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="5749a-115">Di seguito è riportato un esempio che supporta sia Microsoft SQL Server che PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="5749a-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="5749a-116">Questo approccio funziona solo se si conoscono tutti i provider in cui verrà applicata l'operazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="5749a-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="5749a-117">Uso di un MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="5749a-117">Using a MigrationOperation</span></span>

<span data-ttu-id="5749a-118">Per separare l'operazione personalizzata da SQL, è possibile definirne una personalizzata `MigrationOperation` per rappresentarla.</span><span class="sxs-lookup"><span data-stu-id="5749a-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="5749a-119">L'operazione viene quindi passata al provider in modo da poter determinare il SQL appropriato da generare.</span><span class="sxs-lookup"><span data-stu-id="5749a-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="5749a-120">Con questo approccio, il metodo di estensione deve semplicemente aggiungere una di queste operazioni a `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="5749a-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="5749a-121">Questo approccio richiede che ogni provider sappia come generare SQL per questa operazione nel `IMigrationsSqlGenerator` servizio.</span><span class="sxs-lookup"><span data-stu-id="5749a-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="5749a-122">Di seguito è riportato un esempio che esegue l'override del generatore del SQL Server per gestire la nuova operazione.</span><span class="sxs-lookup"><span data-stu-id="5749a-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="5749a-123">Sostituire il servizio Generatore SQL per migrazioni predefinite con quello aggiornato.</span><span class="sxs-lookup"><span data-stu-id="5749a-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
