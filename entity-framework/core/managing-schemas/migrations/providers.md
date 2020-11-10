---
title: Migrazioni con più provider-EF Core
description: Utilizzo delle migrazioni per gestire gli schemi di database quando sono destinati a più provider di database con Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429780"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="c612f-103">Migrazioni con più provider</span><span class="sxs-lookup"><span data-stu-id="c612f-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="c612f-104">Gli [strumenti di EF Core](xref:core/cli/index) solo le migrazioni con impalcature per il provider attivo.</span><span class="sxs-lookup"><span data-stu-id="c612f-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="c612f-105">In alcuni casi, tuttavia, potrebbe essere necessario usare più di un provider (ad esempio Microsoft SQL Server e SQLite) con la DbContext.</span><span class="sxs-lookup"><span data-stu-id="c612f-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="c612f-106">Gestire questo problema gestendo più set di migrazioni, uno per ogni provider, e aggiungendo una migrazione a ogni modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="c612f-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="c612f-107">Uso di più tipi di contesto</span><span class="sxs-lookup"><span data-stu-id="c612f-107">Using multiple context types</span></span>

<span data-ttu-id="c612f-108">Un modo per creare più set di migrazioni consiste nell'usare un tipo DbContext per provider.</span><span class="sxs-lookup"><span data-stu-id="c612f-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="c612f-109">Specificare il tipo di contesto quando si aggiungono nuove migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c612f-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c612f-110">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c612f-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="c612f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c612f-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="c612f-112">Non è necessario specificare la directory di output per le successive migrazioni perché vengono create come elementi di pari livello rispetto all'ultima.</span><span class="sxs-lookup"><span data-stu-id="c612f-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="c612f-113">Uso di un tipo di contesto</span><span class="sxs-lookup"><span data-stu-id="c612f-113">Using one context type</span></span>

<span data-ttu-id="c612f-114">È anche possibile usare un tipo DbContext.</span><span class="sxs-lookup"><span data-stu-id="c612f-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="c612f-115">Questa operazione richiede attualmente lo spostamento delle migrazioni in un assembly separato.</span><span class="sxs-lookup"><span data-stu-id="c612f-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="c612f-116">Per istruzioni sulla configurazione dei progetti, vedere [utilizzo di un progetto di migrazioni separate](xref:core/managing-schemas/migrations/projects) .</span><span class="sxs-lookup"><span data-stu-id="c612f-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="c612f-117">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="c612f-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="c612f-118">A partire da EF Core 5,0, è possibile passare argomenti nell'app dagli strumenti.</span><span class="sxs-lookup"><span data-stu-id="c612f-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="c612f-119">Questo può consentire un flusso di lavoro più semplificato che evita di dover apportare modifiche manuali al progetto durante l'esecuzione degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="c612f-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="c612f-120">Di seguito è riportato un modello che funziona bene quando si usa un [host generico](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c612f-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="c612f-121">Poiché il generatore host predefinito legge la configurazione dagli argomenti della riga di comando, è possibile specificare il provider durante l'esecuzione degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="c612f-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c612f-122">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c612f-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="c612f-123">Il `--` token indirizza `dotnet ef` a trattare tutti gli elementi che seguono come argomento e non tenta di analizzarli come opzioni.</span><span class="sxs-lookup"><span data-stu-id="c612f-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="c612f-124">Qualsiasi argomento aggiuntivo non usato da `dotnet ef` viene inviato all'app.</span><span class="sxs-lookup"><span data-stu-id="c612f-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c612f-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c612f-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="c612f-126">La possibilità di specificare argomenti aggiuntivi per l'app è stata aggiunta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="c612f-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="c612f-127">Se si usa una versione precedente, specificare invece i valori di configurazione con le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="c612f-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
