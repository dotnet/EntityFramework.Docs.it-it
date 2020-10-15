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
# <a name="using-a-separate-migrations-project"></a>Uso di un progetto di migrazioni separate

Potrebbe essere necessario archiviare le migrazioni in un assembly diverso da quello contenente il `DbContext` . È anche possibile usare questa strategia per gestire più set di migrazioni, ad esempio uno per lo sviluppo e un altro per gli aggiornamenti di versione a rilascio.

Per

1. Creare una nuova libreria di classi.

2. Aggiungere un riferimento all'assembly DbContext.

3. Spostare le migrazioni e i file di snapshot del modello nella libreria di classi.
   > [!TIP]
   > Se non si dispone di migrazioni esistenti, generarne una nel progetto che contiene il DbContext quindi spostarlo.
   > Questo è importante perché se l'assembly delle migrazioni non contiene una migrazione esistente, il comando Add-Migration non sarà in grado di trovare DbContext.

4. Configurare l'assembly delle migrazioni:

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. Aggiungere un riferimento all'assembly Migrations dall'assembly di avvio.
   * Se causa una dipendenza circolare, aggiornare il percorso di output della libreria di classi:

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

Se tutte le operazioni sono state effettuate correttamente, dovrebbe essere possibile aggiungere nuove migrazioni al progetto.

## <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
