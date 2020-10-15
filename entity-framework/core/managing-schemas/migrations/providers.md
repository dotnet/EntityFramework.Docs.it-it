---
title: Migrazioni con più provider-EF Core
description: Utilizzo delle migrazioni per gestire gli schemi di database quando sono destinati a più provider di database con Entity Framework Core
author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: f44abb5156ea3a175c68c1a0ec23ff41a9d13452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061983"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="3fcb0-103">Migrazioni con più provider</span><span class="sxs-lookup"><span data-stu-id="3fcb0-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="3fcb0-104">Gli [strumenti di EF Core][1] solo le migrazioni con impalcature per il provider attivo.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-104">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="3fcb0-105">In alcuni casi, tuttavia, potrebbe essere necessario usare più di un provider (ad esempio Microsoft SQL Server e SQLite) con la DbContext.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="3fcb0-106">Esistono due modi per gestire questo problema con le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-106">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="3fcb0-107">È possibile gestire due set di migrazioni, uno per ogni provider, oppure unirli in un unico set che può funzionare in entrambi.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-107">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="3fcb0-108">Due set di migrazione</span><span class="sxs-lookup"><span data-stu-id="3fcb0-108">Two migration sets</span></span>

<span data-ttu-id="3fcb0-109">Nel primo approccio vengono generate due migrazioni per ogni modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-109">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="3fcb0-110">Un modo per eseguire questa operazione è inserire ogni set di migrazione [in un assembly separato][2] e cambiare manualmente il provider attivo (e l'assembly delle migrazioni) tra l'aggiunta delle due migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-110">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="3fcb0-111">Un altro approccio che rende più semplice l'utilizzo degli strumenti consiste nel creare un nuovo tipo che deriva dalla DbContext ed esegue l'override del provider attivo.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-111">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="3fcb0-112">Questo tipo viene utilizzato in fase di progettazione quando si aggiungono o si applicano migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-112">This type is used at design time when adding or applying migrations.</span></span>

```csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="3fcb0-113">Poiché ogni set di migrazione usa i propri tipi DbContext, questo approccio non richiede l'uso di un assembly di migrazioni separato.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-113">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="3fcb0-114">Quando si aggiunge una nuova migrazione, specificare i tipi di contesto.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-114">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3fcb0-115">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3fcb0-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="3fcb0-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fcb0-116">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="3fcb0-117">Non è necessario specificare la directory di output per le successive migrazioni perché vengono create come elementi di pari livello rispetto all'ultima.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-117">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="3fcb0-118">Un set di migrazione</span><span class="sxs-lookup"><span data-stu-id="3fcb0-118">One migration set</span></span>

<span data-ttu-id="3fcb0-119">Se non si vuole avere due set di migrazioni, è possibile combinarli manualmente in un singolo set che può essere applicato a entrambi i provider.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-119">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="3fcb0-120">Le annotazioni possono coesistere perché un provider ignora le annotazioni non riconoscenti.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-120">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="3fcb0-121">Ad esempio, una colonna chiave primaria che funziona sia con Microsoft SQL Server che SQLite potrebbe avere un aspetto simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="3fcb0-121">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

```csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="3fcb0-122">Se le operazioni possono essere applicate solo a un provider o sono diverse tra i provider, utilizzare la `ActiveProvider` proprietà per determinare quale provider è attivo:</span><span class="sxs-lookup"><span data-stu-id="3fcb0-122">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

```csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
