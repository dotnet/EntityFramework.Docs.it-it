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
# <a name="using-a-separate-migrations-project"></a>Uso di un progetto di migrazioni separate

Potrebbe essere necessario archiviare le migrazioni in un progetto diverso da quello contenente il `DbContext` . È anche possibile usare questa strategia per gestire più set di migrazioni, ad esempio uno per lo sviluppo e un altro per gli aggiornamenti di versione a rilascio.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations) di questo articolo in GitHub.

## <a name="steps"></a>Passaggi

1. Creare una nuova libreria di classi.

2. Aggiungere un riferimento al progetto DbContext.

3. Spostare le migrazioni e i file di snapshot del modello nella libreria di classi.
   > [!TIP]
   > Se non si dispone di migrazioni esistenti, generarne una nel progetto che contiene il DbContext quindi spostarlo.
   > Questo è importante perché se il progetto Migrations non contiene una migrazione esistente, il comando Add-Migration non sarà in grado di trovare DbContext.

4. Configurare l'assembly delle migrazioni:

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. Aggiungere un riferimento al progetto Migrations dal progetto di **avvio** .

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   Se questo genera una dipendenza circolare, è possibile aggiornare invece il percorso di output di base del progetto **Migrations** :

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

Se tutte le operazioni sono state effettuate correttamente, dovrebbe essere possibile aggiungere nuove migrazioni al progetto.

## <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
