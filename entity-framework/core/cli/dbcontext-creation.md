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
# <a name="design-time-dbcontext-creation"></a>Creazione di DbContext in fase di progettazione

Alcuni dei comandi degli strumenti di EF Core (ad esempio, i comandi delle [migrazioni][1] ) richiedono la creazione di un'istanza derivata in fase di `DbContext` progettazione per raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database. Nella maggior parte dei casi, è preferibile che il `DbContext` creato sia configurato in modo analogo a come verrebbe [configurato][2]in fase di esecuzione.

Esistono diversi modi in cui gli strumenti tentano di creare `DbContext` :

## <a name="from-application-services"></a>Da servizi applicazioni

Se il progetto di avvio usa il [ASP.NET Core host Web][3] o l' [host generico .NET Core][4], gli strumenti tentano di ottenere l'oggetto DbContext dal provider di servizi dell'applicazione.

Per prima cosa gli strumenti tentano di ottenere il provider di servizi richiamando, `Program.CreateHostBuilder()` `Build()` quindi accedendo alla `Services` Proprietà.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> Quando si crea una nuova applicazione ASP.NET Core, questo hook è incluso per impostazione predefinita.

La `DbContext` stessa e tutte le dipendenze nel costruttore devono essere registrate come servizi nel provider di servizi dell'applicazione. Questa operazione può essere eseguita facilmente con [un costruttore su `DbContext` che accetta un'istanza di `DbContextOptions<TContext>` come argomento][5] e usando il [ `AddDbContext<TContext>` Metodo][6].

## <a name="using-a-constructor-with-no-parameters"></a>Uso di un costruttore senza parametri

Se il DbContext non può essere ottenuto dal provider di servizi dell'applicazione, gli strumenti cercano il `DbContext` tipo derivato all'interno del progetto. Tentano quindi di creare un'istanza usando un costruttore senza parametri. Può essere il costruttore predefinito se `DbContext` è configurato utilizzando il [`OnConfiguring`][7] metodo.

## <a name="from-a-design-time-factory"></a>Da una factory della fase di progettazione

È anche possibile indicare agli strumenti come creare il DbContext implementando l' `IDesignTimeDbContextFactory<TContext>` interfaccia: se una classe che implementa questa interfaccia si trova nello stesso progetto dell'oggetto derivato `DbContext` o nel progetto di avvio dell'applicazione, gli strumenti ignorano le altre modalità di creazione del DbContext e usano invece la factory della fase di progettazione.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> Prima di EFCore 5,0 il `args` parametro non è stato usato (vedere [questo problema][8]).
> Questo problema è stato risolto in EFCore 5,0 e gli eventuali argomenti della fase di progettazione aggiuntivi vengono passati all'applicazione tramite il parametro.

Una factory della fase di progettazione può essere particolarmente utile se è necessario configurare in `DbContext` modo diverso per la fase di progettazione rispetto alla fase di esecuzione, se il `DbContext` costruttore accetta parametri aggiuntivi non viene registrato in di, se non si usa o se per qualche motivo si preferisce non avere un `CreateHostBuilder` metodo nella classe dell'applicazione ASP.NET Core `Main` .

## <a name="args"></a>Args

Sia <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType> che `Program.CreateHostBuilder` accettano argomenti della riga di comando.

A partire da EF Core 5,0, è possibile specificare questi argomenti dagli strumenti:

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

Il `--` token indirizza `dotnet ef` a trattare tutti gli elementi che seguono come argomento e non tenta di analizzarli come opzioni. Qualsiasi argomento aggiuntivo non usato da `dotnet ef` viene inviato all'app.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

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
