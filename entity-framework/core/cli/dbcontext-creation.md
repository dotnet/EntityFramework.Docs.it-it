---
title: Creazione DbContext della fase di progettazione-EF Core
description: Strategie per la creazione di un DbContext in fase di progettazione con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: 1a2c0e853047cf4ab54a320d0bef413a114e90bc
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543406"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="6a811-103">Creazione di DbContext in fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="6a811-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="6a811-104">Alcuni dei comandi degli strumenti di EF Core (ad esempio, i comandi delle [migrazioni][1] ) richiedono la creazione di un'istanza derivata in fase di `DbContext` progettazione per raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database.</span><span class="sxs-lookup"><span data-stu-id="6a811-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="6a811-105">Nella maggior parte dei casi, è preferibile che il `DbContext` creato sia configurato in modo analogo a come verrebbe [configurato][2]in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="6a811-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="6a811-106">Esistono diversi modi in cui gli strumenti tentano di creare `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="6a811-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="6a811-107">Da servizi applicazioni</span><span class="sxs-lookup"><span data-stu-id="6a811-107">From application services</span></span>

<span data-ttu-id="6a811-108">Se il progetto di avvio usa il [ASP.NET Core host Web][3] o l' [host generico .NET Core][4], gli strumenti tentano di ottenere l'oggetto DbContext dal provider di servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6a811-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="6a811-109">Per prima cosa gli strumenti tentano di ottenere il provider di servizi richiamando, `Program.CreateHostBuilder()` `Build()` quindi accedendo alla `Services` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="6a811-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> <span data-ttu-id="6a811-110">Quando si crea una nuova applicazione ASP.NET Core, questo hook è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="6a811-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="6a811-111">La `DbContext` stessa e tutte le dipendenze nel costruttore devono essere registrate come servizi nel provider di servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6a811-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="6a811-112">Questa operazione può essere eseguita facilmente con [un costruttore su `DbContext` che accetta un'istanza di `DbContextOptions<TContext>` come argomento][5] e usando il [ `AddDbContext<TContext>` Metodo][6].</span><span class="sxs-lookup"><span data-stu-id="6a811-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="6a811-113">Uso di un costruttore senza parametri</span><span class="sxs-lookup"><span data-stu-id="6a811-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="6a811-114">Se il DbContext non può essere ottenuto dal provider di servizi dell'applicazione, gli strumenti cercano il `DbContext` tipo derivato all'interno del progetto.</span><span class="sxs-lookup"><span data-stu-id="6a811-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="6a811-115">Tentano quindi di creare un'istanza usando un costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="6a811-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="6a811-116">Può essere il costruttore predefinito se `DbContext` è configurato utilizzando il [`OnConfiguring`][7] metodo.</span><span class="sxs-lookup"><span data-stu-id="6a811-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="6a811-117">Da una factory della fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="6a811-117">From a design-time factory</span></span>

<span data-ttu-id="6a811-118">È anche possibile indicare agli strumenti come creare il DbContext implementando l' `IDesignTimeDbContextFactory<TContext>` interfaccia: se una classe che implementa questa interfaccia si trova nello stesso progetto dell'oggetto derivato `DbContext` o nel progetto di avvio dell'applicazione, gli strumenti ignorano le altre modalità di creazione del DbContext e usano invece la factory della fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="6a811-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> <span data-ttu-id="6a811-119">Prima di EFCore 5,0 il `args` parametro non è stato usato (vedere [questo problema][8]).</span><span class="sxs-lookup"><span data-stu-id="6a811-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="6a811-120">Questo problema è stato risolto in EFCore 5,0 e gli eventuali argomenti della fase di progettazione aggiuntivi vengono passati all'applicazione tramite il parametro.</span><span class="sxs-lookup"><span data-stu-id="6a811-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="6a811-121">Una factory della fase di progettazione può essere particolarmente utile se è necessario configurare in `DbContext` modo diverso per la fase di progettazione rispetto alla fase di esecuzione, se il `DbContext` costruttore accetta parametri aggiuntivi non viene registrato in di, se non si usa o se per qualche motivo si preferisce non avere un `CreateHostBuilder` metodo nella classe dell'applicazione ASP.NET Core `Main` .</span><span class="sxs-lookup"><span data-stu-id="6a811-121">A design-time factory can be especially useful if you need to configure the `DbContext` differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `CreateHostBuilder` method in your ASP.NET Core application's `Main` class.</span></span>

## <a name="args"></a><span data-ttu-id="6a811-122">Args</span><span class="sxs-lookup"><span data-stu-id="6a811-122">Args</span></span>

<span data-ttu-id="6a811-123">Sia <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType> che `Program.CreateHostBuilder` accettano argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="6a811-123">Both <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType> and `Program.CreateHostBuilder` accept command line arguments.</span></span>

<span data-ttu-id="6a811-124">A partire da EF Core 5,0, è possibile specificare questi argomenti dagli strumenti:</span><span class="sxs-lookup"><span data-stu-id="6a811-124">Starting in EF Core 5.0, you can specify these arguments from the tools:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6a811-125">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6a811-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

<span data-ttu-id="6a811-126">Il `--` token indirizza `dotnet ef` a trattare tutti gli elementi che seguono come argomento e non tenta di analizzarli come opzioni.</span><span class="sxs-lookup"><span data-stu-id="6a811-126">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="6a811-127">Qualsiasi argomento aggiuntivo non usato da `dotnet ef` viene inviato all'app.</span><span class="sxs-lookup"><span data-stu-id="6a811-127">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="6a811-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a811-128">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
