---
title: Uso di un progetto di migrazioni separate-EF Core
description: Utilizzo di un progetto di migrazione separato per la gestione degli schemi di database con Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062490"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="f6dba-103">Uso di un progetto di migrazioni separate</span><span class="sxs-lookup"><span data-stu-id="f6dba-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="f6dba-104">Potrebbe essere necessario archiviare le migrazioni in un assembly diverso da quello contenente il `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f6dba-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="f6dba-105">È anche possibile usare questa strategia per gestire più set di migrazioni, ad esempio uno per lo sviluppo e un altro per gli aggiornamenti di versione a rilascio.</span><span class="sxs-lookup"><span data-stu-id="f6dba-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="f6dba-106">Per</span><span class="sxs-lookup"><span data-stu-id="f6dba-106">To do this...</span></span>

1. <span data-ttu-id="f6dba-107">Creare una nuova libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="f6dba-107">Create a new class library.</span></span>

2. <span data-ttu-id="f6dba-108">Aggiungere un riferimento all'assembly DbContext.</span><span class="sxs-lookup"><span data-stu-id="f6dba-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="f6dba-109">Spostare le migrazioni e i file di snapshot del modello nella libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="f6dba-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="f6dba-110">Se non si dispone di migrazioni esistenti, generarne una nel progetto che contiene il DbContext quindi spostarlo.</span><span class="sxs-lookup"><span data-stu-id="f6dba-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="f6dba-111">Questo è importante perché se l'assembly delle migrazioni non contiene una migrazione esistente, il comando Add-Migration non sarà in grado di trovare DbContext.</span><span class="sxs-lookup"><span data-stu-id="f6dba-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="f6dba-112">Configurare l'assembly delle migrazioni:</span><span class="sxs-lookup"><span data-stu-id="f6dba-112">Configure the migrations assembly:</span></span>

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="f6dba-113">Aggiungere un riferimento all'assembly Migrations dall'assembly di avvio.</span><span class="sxs-lookup"><span data-stu-id="f6dba-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="f6dba-114">Se causa una dipendenza circolare, aggiornare il percorso di output della libreria di classi:</span><span class="sxs-lookup"><span data-stu-id="f6dba-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="f6dba-115">Se tutte le operazioni sono state effettuate correttamente, dovrebbe essere possibile aggiungere nuove migrazioni al progetto.</span><span class="sxs-lookup"><span data-stu-id="f6dba-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="f6dba-116">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6dba-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="f6dba-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6dba-117">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
