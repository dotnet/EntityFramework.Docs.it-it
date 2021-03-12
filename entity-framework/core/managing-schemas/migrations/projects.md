---
title: Uso di un progetto di migrazioni separate-EF Core
description: Utilizzo di un progetto di migrazione separato per la gestione degli schemi di database con Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 9a6b8977f9d7bcdae0fb9aea6966a7eb43e9e7db
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024147"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="09b98-103">Uso di un progetto di migrazioni separate</span><span class="sxs-lookup"><span data-stu-id="09b98-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="09b98-104">Potrebbe essere necessario archiviare le migrazioni in un progetto diverso da quello contenente il `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="09b98-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="09b98-105">È anche possibile usare questa strategia per gestire più set di migrazioni, ad esempio uno per lo sviluppo e un altro per gli aggiornamenti di versione a rilascio.</span><span class="sxs-lookup"><span data-stu-id="09b98-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="09b98-106">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="09b98-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="09b98-107">Passaggi</span><span class="sxs-lookup"><span data-stu-id="09b98-107">Steps</span></span>

1. <span data-ttu-id="09b98-108">Creare una nuova libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="09b98-108">Create a new class library.</span></span>

2. <span data-ttu-id="09b98-109">Aggiungere un riferimento al progetto DbContext.</span><span class="sxs-lookup"><span data-stu-id="09b98-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="09b98-110">Spostare le migrazioni e i file di snapshot del modello nella libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="09b98-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="09b98-111">Se non si dispone di migrazioni esistenti, generarne una nel progetto che contiene il DbContext quindi spostarlo.</span><span class="sxs-lookup"><span data-stu-id="09b98-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="09b98-112">Questo è importante perché se il progetto Migrations non contiene una migrazione esistente, il comando Add-Migration non sarà in grado di trovare DbContext.</span><span class="sxs-lookup"><span data-stu-id="09b98-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="09b98-113">Configurare l'assembly delle migrazioni:</span><span class="sxs-lookup"><span data-stu-id="09b98-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="09b98-114">Aggiungere un riferimento al progetto Migrations dal progetto di **avvio** .</span><span class="sxs-lookup"><span data-stu-id="09b98-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="09b98-115">Se questo genera una dipendenza circolare, è possibile aggiornare invece il percorso di output di base del progetto **Migrations** :</span><span class="sxs-lookup"><span data-stu-id="09b98-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="09b98-116">Se tutte le operazioni sono state effettuate correttamente, dovrebbe essere possibile aggiungere nuove migrazioni al progetto.</span><span class="sxs-lookup"><span data-stu-id="09b98-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="09b98-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="09b98-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="09b98-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b98-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
