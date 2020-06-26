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
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>Aggiornamento da EF Core 1,0 RC1 a 1,0 RC2

Questo articolo fornisce indicazioni per lo trasferimento di un'applicazione compilata con i pacchetti RC1 in RC2.

## <a name="package-names-and-versions"></a>Nomi e versioni del pacchetto

Tra RC1 e RC2, è stato modificato da "Entity Framework 7" a "Entity Framework Core". Per altre informazioni sui motivi della modifica apportata a [questo post di Scott hanselt](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx), vedere. A causa di questa modifica, i nomi dei pacchetti sono stati modificati da `EntityFramework.*` a `Microsoft.EntityFrameworkCore.*` e dalle versioni da `7.0.0-rc1-final` a `1.0.0-rc2-final` (o `1.0.0-preview1-final` per gli strumenti).

**Sarà necessario rimuovere completamente i pacchetti RC1 e quindi installare RC2.** Ecco il mapping per alcuni pacchetti comuni.

| Pacchetto RC1                                               | Equivalente RC2                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. MicrosoftSqlServer 7.0.0-RC1-Final | Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2-Final      |
| EntityFramework. SQLite 7.0.0-RC1-Final | Microsoft. EntityFrameworkCore. SQLite 1.0.0-RC2-Final      |
| EntityFramework7. npgsql 3.1.0-RC1-3     | NpgSql. EntityFrameworkCore. Postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-RC1-Final | EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2-Final      |
| EntityFramework. SqlServerCompact40 7.0.0-RC1-Final | EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2-Final      |
| EntityFramework. InMemory 7.0.0-RC1-Final | Microsoft. EntityFrameworkCore. InMemory 1.0.0-RC2-Final      |
| EntityFramework. IBMDataServer 7.0.0-beta1     | Non ancora disponibile per RC2                                            |
| EntityFramework. Commands 7.0.0-RC1-Final | Microsoft. EntityFrameworkCore. Tools 1.0.0-preview1-Final |
| EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1-Final | Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2-Final      |

## <a name="namespaces"></a>Spazi dei nomi

Insieme ai nomi di pacchetto, gli spazi dei nomi sono stati modificati da `Microsoft.Data.Entity.*` a `Microsoft.EntityFrameworkCore.*` . È possibile gestire questa modifica con una ricerca/sostituzione di `using Microsoft.Data.Entity` con `using Microsoft.EntityFrameworkCore` .

## <a name="table-naming-convention-changes"></a>Modifiche della convenzione di denominazione delle tabelle

Una modifica funzionale significativa introdotta in RC2 consiste nell'usare il nome della `DbSet<TEntity>` proprietà per un'entità specificata come nome della tabella a cui viene eseguito il mapping, anziché solo il nome della classe. Per ulteriori informazioni su questa modifica, vedere [il problema relativo all'annuncio](https://github.com/aspnet/Announcements/issues/167).

Per le applicazioni RC1 esistenti, è consigliabile aggiungere il codice seguente all'inizio del `OnModelCreating` metodo per rispettare la strategia di denominazione RC1:

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

Se si desidera adottare la nuova strategia di denominazione, è consigliabile completare correttamente il resto dei passaggi di aggiornamento e quindi rimuovere il codice e creare una migrazione per applicare la tabella Rinomina.

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>Modifiche a AddDbContext/Startup.cs (solo progetti ASP.NET Core)

Nella versione RC1 era necessario aggiungere Entity Framework Services al provider di servizi dell'applicazione `Startup.ConfigureServices(...)` :

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

In RC2 è possibile rimuovere le chiamate a `AddEntityFramework()` , `AddSqlServer()` e così via:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

È anche necessario aggiungere un costruttore, al contesto derivato, che accetta le opzioni di contesto e le passa al costruttore di base. Questa operazione è necessaria perché è stata rimossa una magia spaventosa che è stata introdotta in background:

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>Passaggio di un oggetto IServiceProvider

Se si dispone di codice RC1 che passa un oggetto `IServiceProvider` al contesto, ora è stato spostato in `DbContextOptions` , anziché essere un parametro del costruttore separato. Utilizzare `DbContextOptionsBuilder.UseInternalServiceProvider(...)` per impostare il provider di servizi.

### <a name="testing"></a>Test

Lo scenario più comune è quello di controllare l'ambito di un database in memoria durante il test. Per un esempio di come eseguire questa operazione con RC2, vedere l'articolo sui [test](testing/index.md) aggiornati.

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>Risoluzione di servizi interni da un provider di servizi dell'applicazione (solo ASP.NET Core progetti)

Se si dispone di un'applicazione ASP.NET Core e si desidera che EF risolvono servizi interni dal provider di servizi dell'applicazione, è disponibile un overload di `AddDbContext` che consente di configurare quanto segue:

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> È consigliabile consentire a EF di gestire internamente i propri servizi, a meno che non esista un motivo per combinare i servizi EF interni nel provider di servizi dell'applicazione. Il motivo principale per cui si vuole eseguire questa operazione consiste nell'usare il provider di servizi dell'applicazione per sostituire i servizi che EF usa internamente

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>Comandi DNX => interfaccia della riga di comando di .NET Core (solo ASP.NET Core progetti)

Se in precedenza sono stati usati i `dnx ef` comandi per i progetti ASP.NET 5, questi sono ora spostati nei `dotnet ef` comandi. Viene comunque applicata la stessa sintassi del comando. `dotnet ef --help`Per informazioni sulla sintassi, è possibile usare.

Il modo in cui i comandi sono registrati è stato modificato in RC2, a causa di DNX sostituito dal interfaccia della riga di comando di .NET Core. I comandi sono ora registrati in una `tools` sezione di `project.json` :

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
> Se si usa Visual Studio, è ora possibile usare la console di gestione pacchetti per eseguire i comandi EF per i progetti ASP.NET Core (questa operazione non è supportata nella versione RC1). Per eseguire questa operazione, è comunque necessario registrare i comandi nella `tools` sezione di `project.json` .

## <a name="package-manager-commands-require-powershell-5"></a>I comandi di gestione pacchetti richiedono PowerShell 5

Se si usano i comandi Entity Framework nella console di gestione pacchetti in Visual Studio, sarà necessario assicurarsi che sia installato PowerShell 5. Si tratta di un requisito temporaneo che verrà rimosso nella prossima versione (vedere il [problema #5327](https://github.com/aspnet/EntityFramework/issues/5327) per altri dettagli).

## <a name="using-imports-in-projectjson"></a>Uso di "Imports" in project.json

Alcune dipendenze di EF Core non supportano ancora .NET Standard. EF Core in .NET Standard e nei progetti .NET Core può essere necessario aggiungere "Imports" project.json come soluzione temporanea.

Quando si aggiunge EF, NuGet Restore Visualizza questo messaggio di errore:

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

La soluzione alternativa consiste nell'importare manualmente il profilo portatile "Portable-net451 + Win8". Questa operazione impone a NuGet di trattare i file binari che corrispondono a questo metodo fornito come Framework compatibile con .NET Standard, anche se non lo sono. Anche se "Portable-net451 + Win8" non è compatibile con il 100% con .NET Standard, è sufficientemente compatibile per la transizione da PCL a .NET Standard. Le importazioni possono essere rimosse quando le dipendenze di Entity Framework vengono aggiornate alla .NET Standard.

È possibile aggiungere più Framework a "Imports" nella sintassi di matrice. Potrebbero essere necessarie altre importazioni se si aggiungono altre librerie al progetto.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

Vedere [#5176 di problema](https://github.com/aspnet/EntityFramework/issues/5176).
