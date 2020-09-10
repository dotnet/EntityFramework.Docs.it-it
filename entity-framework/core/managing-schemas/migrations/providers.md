---
title: Migrazioni con più provider-EF Core
description: Utilizzo delle migrazioni per gestire gli schemi di database quando sono destinati a più provider di database con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: df38af6ac700a530894b98e1f29bbe804831bad5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619181"
---
# <a name="migrations-with-multiple-providers"></a>Migrazioni con più provider

Gli [strumenti di EF Core][1] solo le migrazioni con impalcature per il provider attivo. In alcuni casi, tuttavia, potrebbe essere necessario usare più di un provider (ad esempio Microsoft SQL Server e SQLite) con la DbContext. Esistono due modi per gestire questo problema con le migrazioni. È possibile gestire due set di migrazioni, uno per ogni provider, oppure unirli in un unico set che può funzionare in entrambi.

## <a name="two-migration-sets"></a>Due set di migrazione

Nel primo approccio vengono generate due migrazioni per ogni modifica del modello.

Un modo per eseguire questa operazione è inserire ogni set di migrazione [in un assembly separato][2] e cambiare manualmente il provider attivo (e l'assembly delle migrazioni) tra l'aggiunta delle due migrazioni.

Un altro approccio che rende più semplice l'utilizzo degli strumenti consiste nel creare un nuovo tipo che deriva dalla DbContext ed esegue l'override del provider attivo. Questo tipo viene utilizzato in fase di progettazione quando si aggiungono o si applicano migrazioni.

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> Poiché ogni set di migrazione usa i propri tipi DbContext, questo approccio non richiede l'uso di un assembly di migrazioni separato.

Quando si aggiunge una nuova migrazione, specificare i tipi di contesto.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Non è necessario specificare la directory di output per le successive migrazioni perché vengono create come elementi di pari livello rispetto all'ultima.

## <a name="one-migration-set"></a>Un set di migrazione

Se non si vuole avere due set di migrazioni, è possibile combinarli manualmente in un singolo set che può essere applicato a entrambi i provider.

Le annotazioni possono coesistere perché un provider ignora le annotazioni non riconoscenti. Ad esempio, una colonna chiave primaria che funziona sia con Microsoft SQL Server che SQLite potrebbe avere un aspetto simile al seguente.

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

Se le operazioni possono essere applicate solo a un provider o sono diverse tra i provider, utilizzare la `ActiveProvider` proprietà per determinare quale provider è attivo:

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
