---
title: Aggiornamento da EF Core 1,0 RC1 a RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370526"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="93cfc-102">Aggiornamento da EF Core 1,0 RC1 a 1,0 RC2</span><span class="sxs-lookup"><span data-stu-id="93cfc-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="93cfc-103">Questo articolo fornisce indicazioni per lo trasferimento di un'applicazione compilata con i pacchetti RC1 in RC2.</span><span class="sxs-lookup"><span data-stu-id="93cfc-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="93cfc-104">Nomi e versioni del pacchetto</span><span class="sxs-lookup"><span data-stu-id="93cfc-104">Package Names and Versions</span></span>

<span data-ttu-id="93cfc-105">Tra RC1 e RC2, è stato modificato da "Entity Framework 7" a "Entity Framework Core".</span><span class="sxs-lookup"><span data-stu-id="93cfc-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="93cfc-106">Per altre informazioni sui motivi della modifica apportata a [questo post di Scott hanselt](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx), vedere.</span><span class="sxs-lookup"><span data-stu-id="93cfc-106">You can read more about the reasons for the change in [this post by Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="93cfc-107">A causa di questa modifica, i nomi dei pacchetti sono stati modificati da `EntityFramework.*` a `Microsoft.EntityFrameworkCore.*` e dalle versioni da `7.0.0-rc1-final` a `1.0.0-rc2-final` (o `1.0.0-preview1-final` per gli strumenti).</span><span class="sxs-lookup"><span data-stu-id="93cfc-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="93cfc-108">**Sarà necessario rimuovere completamente i pacchetti RC1 e quindi installare RC2.**</span><span class="sxs-lookup"><span data-stu-id="93cfc-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="93cfc-109">Ecco il mapping per alcuni pacchetti comuni.</span><span class="sxs-lookup"><span data-stu-id="93cfc-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="93cfc-110">Pacchetto RC1</span><span class="sxs-lookup"><span data-stu-id="93cfc-110">RC1 Package</span></span>                                               | <span data-ttu-id="93cfc-111">Equivalente RC2</span><span class="sxs-lookup"><span data-stu-id="93cfc-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="93cfc-112">EntityFramework. MicrosoftSqlServer 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-113">Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="93cfc-114">EntityFramework. SQLite 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-115">Microsoft. EntityFrameworkCore. SQLite 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="93cfc-116">EntityFramework7. npgsql 3.1.0-RC1-3</span><span class="sxs-lookup"><span data-stu-id="93cfc-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="93cfc-117">NpgSql. EntityFrameworkCore. Postgres<to be advised></span><span class="sxs-lookup"><span data-stu-id="93cfc-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="93cfc-118">EntityFramework. SqlServerCompact35 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-119">EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="93cfc-120">EntityFramework. SqlServerCompact40 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-121">EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="93cfc-122">EntityFramework. InMemory 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-123">Microsoft. EntityFrameworkCore. InMemory 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="93cfc-124">EntityFramework. IBMDataServer 7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="93cfc-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="93cfc-125">Non ancora disponibile per RC2</span><span class="sxs-lookup"><span data-stu-id="93cfc-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="93cfc-126">EntityFramework. Commands 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-127">Microsoft. EntityFrameworkCore. Tools 1.0.0-preview1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="93cfc-128">EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="93cfc-129">Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2-Final</span><span class="sxs-lookup"><span data-stu-id="93cfc-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="93cfc-130">Spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="93cfc-130">Namespaces</span></span>

<span data-ttu-id="93cfc-131">Insieme ai nomi di pacchetto, gli spazi dei nomi sono stati modificati da `Microsoft.Data.Entity.*` a `Microsoft.EntityFrameworkCore.*` .</span><span class="sxs-lookup"><span data-stu-id="93cfc-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="93cfc-132">È possibile gestire questa modifica con una ricerca/sostituzione di `using Microsoft.Data.Entity` con `using Microsoft.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="93cfc-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="93cfc-133">Modifiche della convenzione di denominazione delle tabelle</span><span class="sxs-lookup"><span data-stu-id="93cfc-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="93cfc-134">Una modifica funzionale significativa introdotta in RC2 consiste nell'usare il nome della `DbSet<TEntity>` proprietà per un'entità specificata come nome della tabella a cui viene eseguito il mapping, anziché solo il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="93cfc-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="93cfc-135">Per ulteriori informazioni su questa modifica, vedere [il problema relativo all'annuncio](https://github.com/aspnet/Announcements/issues/167).</span><span class="sxs-lookup"><span data-stu-id="93cfc-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="93cfc-136">Per le applicazioni RC1 esistenti, è consigliabile aggiungere il codice seguente all'inizio del `OnModelCreating` metodo per rispettare la strategia di denominazione RC1:</span><span class="sxs-lookup"><span data-stu-id="93cfc-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="93cfc-137">Se si desidera adottare la nuova strategia di denominazione, è consigliabile completare correttamente il resto dei passaggi di aggiornamento e quindi rimuovere il codice e creare una migrazione per applicare la tabella Rinomina.</span><span class="sxs-lookup"><span data-stu-id="93cfc-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="93cfc-138">Modifiche a AddDbContext/Startup.cs (solo progetti ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="93cfc-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="93cfc-139">Nella versione RC1 era necessario aggiungere Entity Framework Services al provider di servizi dell'applicazione `Startup.ConfigureServices(...)` :</span><span class="sxs-lookup"><span data-stu-id="93cfc-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="93cfc-140">In RC2 è possibile rimuovere le chiamate a `AddEntityFramework()` , `AddSqlServer()` e così via:</span><span class="sxs-lookup"><span data-stu-id="93cfc-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="93cfc-141">È anche necessario aggiungere un costruttore, al contesto derivato, che accetta le opzioni di contesto e le passa al costruttore di base.</span><span class="sxs-lookup"><span data-stu-id="93cfc-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="93cfc-142">Questa operazione è necessaria perché è stata rimossa una magia spaventosa che è stata introdotta in background:</span><span class="sxs-lookup"><span data-stu-id="93cfc-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="93cfc-143">Passaggio di un oggetto IServiceProvider</span><span class="sxs-lookup"><span data-stu-id="93cfc-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="93cfc-144">Se si dispone di codice RC1 che passa un oggetto `IServiceProvider` al contesto, ora è stato spostato in `DbContextOptions` , anziché essere un parametro del costruttore separato.</span><span class="sxs-lookup"><span data-stu-id="93cfc-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="93cfc-145">Utilizzare `DbContextOptionsBuilder.UseInternalServiceProvider(...)` per impostare il provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="93cfc-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="93cfc-146">Test</span><span class="sxs-lookup"><span data-stu-id="93cfc-146">Testing</span></span>

<span data-ttu-id="93cfc-147">Lo scenario più comune è quello di controllare l'ambito di un database in memoria durante il test.</span><span class="sxs-lookup"><span data-stu-id="93cfc-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="93cfc-148">Per un esempio di come eseguire questa operazione con RC2, vedere l'articolo sui [test](testing/index.md) aggiornati.</span><span class="sxs-lookup"><span data-stu-id="93cfc-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="93cfc-149">Risoluzione di servizi interni da un provider di servizi dell'applicazione (solo ASP.NET Core progetti)</span><span class="sxs-lookup"><span data-stu-id="93cfc-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="93cfc-150">Se si dispone di un'applicazione ASP.NET Core e si desidera che EF risolvono servizi interni dal provider di servizi dell'applicazione, è disponibile un overload di `AddDbContext` che consente di configurare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="93cfc-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> <span data-ttu-id="93cfc-151">È consigliabile consentire a EF di gestire internamente i propri servizi, a meno che non esista un motivo per combinare i servizi EF interni nel provider di servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="93cfc-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="93cfc-152">Il motivo principale per cui si vuole eseguire questa operazione consiste nell'usare il provider di servizi dell'applicazione per sostituire i servizi che EF usa internamente</span><span class="sxs-lookup"><span data-stu-id="93cfc-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a><span data-ttu-id="93cfc-153">Comandi DNX => interfaccia della riga di comando di .NET Core (solo ASP.NET Core progetti)</span><span class="sxs-lookup"><span data-stu-id="93cfc-153">DNX Commands => .NET Core CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="93cfc-154">Se in precedenza sono stati usati i `dnx ef` comandi per i progetti ASP.NET 5, questi sono ora spostati nei `dotnet ef` comandi.</span><span class="sxs-lookup"><span data-stu-id="93cfc-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="93cfc-155">Viene comunque applicata la stessa sintassi del comando.</span><span class="sxs-lookup"><span data-stu-id="93cfc-155">The same command syntax still applies.</span></span> <span data-ttu-id="93cfc-156">`dotnet ef --help`Per informazioni sulla sintassi, è possibile usare.</span><span class="sxs-lookup"><span data-stu-id="93cfc-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="93cfc-157">Il modo in cui i comandi sono registrati è stato modificato in RC2, a causa di DNX sostituito dal interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="93cfc-157">The way commands are registered has changed in RC2, due to DNX being replaced by the .NET Core CLI.</span></span> <span data-ttu-id="93cfc-158">I comandi sono ora registrati in una `tools` sezione di `project.json` :</span><span class="sxs-lookup"><span data-stu-id="93cfc-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> <span data-ttu-id="93cfc-159">Se si usa Visual Studio, è ora possibile usare la console di gestione pacchetti per eseguire i comandi EF per i progetti ASP.NET Core (questa operazione non è supportata nella versione RC1).</span><span class="sxs-lookup"><span data-stu-id="93cfc-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="93cfc-160">Per eseguire questa operazione, è comunque necessario registrare i comandi nella `tools` sezione di `project.json` .</span><span class="sxs-lookup"><span data-stu-id="93cfc-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="93cfc-161">I comandi di gestione pacchetti richiedono PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="93cfc-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="93cfc-162">Se si usano i comandi Entity Framework nella console di gestione pacchetti in Visual Studio, sarà necessario assicurarsi che sia installato PowerShell 5.</span><span class="sxs-lookup"><span data-stu-id="93cfc-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="93cfc-163">Si tratta di un requisito temporaneo che verrà rimosso nella prossima versione (vedere il [problema #5327](https://github.com/aspnet/EntityFramework/issues/5327) per altri dettagli).</span><span class="sxs-lookup"><span data-stu-id="93cfc-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="93cfc-164">Uso di "Imports" in project.json</span><span class="sxs-lookup"><span data-stu-id="93cfc-164">Using "imports" in project.json</span></span>

<span data-ttu-id="93cfc-165">Alcune dipendenze di EF Core non supportano ancora .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="93cfc-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="93cfc-166">EF Core in .NET Standard e nei progetti .NET Core può essere necessario aggiungere "Imports" project.json come soluzione temporanea.</span><span class="sxs-lookup"><span data-stu-id="93cfc-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="93cfc-167">Quando si aggiunge EF, NuGet Restore Visualizza questo messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="93cfc-167">When adding EF, NuGet restore will display this error message:</span></span>

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

<span data-ttu-id="93cfc-168">La soluzione alternativa consiste nell'importare manualmente il profilo portatile "Portable-net451 + Win8".</span><span class="sxs-lookup"><span data-stu-id="93cfc-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="93cfc-169">Questa operazione impone a NuGet di trattare i file binari che corrispondono a questo metodo fornito come Framework compatibile con .NET Standard, anche se non lo sono.</span><span class="sxs-lookup"><span data-stu-id="93cfc-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="93cfc-170">Anche se "Portable-net451 + Win8" non è compatibile con il 100% con .NET Standard, è sufficientemente compatibile per la transizione da PCL a .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="93cfc-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="93cfc-171">Le importazioni possono essere rimosse quando le dipendenze di Entity Framework vengono aggiornate alla .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="93cfc-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="93cfc-172">È possibile aggiungere più Framework a "Imports" nella sintassi di matrice.</span><span class="sxs-lookup"><span data-stu-id="93cfc-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="93cfc-173">Potrebbero essere necessarie altre importazioni se si aggiungono altre librerie al progetto.</span><span class="sxs-lookup"><span data-stu-id="93cfc-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="93cfc-174">Vedere [#5176 di problema](https://github.com/aspnet/EntityFramework/issues/5176).</span><span class="sxs-lookup"><span data-stu-id="93cfc-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
