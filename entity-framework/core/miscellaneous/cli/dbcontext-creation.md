---
title: Creazione DbContext della fase di progettazione-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672953"
---
# <a name="design-time-dbcontext-creation"></a>Creazione di DbContext in fase di progettazione

Alcuni dei comandi degli strumenti di EF Core (ad esempio, i comandi delle [migrazioni][1] ) richiedono la creazione di un'istanza derivata in fase di `DbContext` progettazione per raccogliere informazioni dettagliate sui tipi di entità dell'applicazione e su come eseguono il mapping a uno schema del database. Nella maggior parte dei casi, è preferibile che il `DbContext` creato sia configurato in modo analogo a come verrebbe [configurato][2]in fase di esecuzione.

Esistono diversi modi in cui gli strumenti tentano di creare `DbContext` :

## <a name="from-application-services"></a>Da servizi applicazioni

Se il progetto di avvio usa il [ASP.NET Core host Web][3] o l' [host generico .NET Core][4], gli strumenti tentano di ottenere l'oggetto DbContext dal provider di servizi dell'applicazione.

Per prima cosa gli strumenti tentano di ottenere il provider di servizi richiamando, `Program.CreateHostBuilder()` `Build()` quindi accedendo alla `Services` Proprietà.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> Quando si crea una nuova applicazione ASP.NET Core, questo hook è incluso per impostazione predefinita.

La `DbContext` stessa e tutte le dipendenze nel costruttore devono essere registrate come servizi nel provider di servizi dell'applicazione. Questa operazione può essere eseguita facilmente con [un costruttore su `DbContext` che accetta un'istanza di `DbContextOptions<TContext>` come argomento][5] e usando il [ `AddDbContext<TContext>` Metodo][6].

## <a name="using-a-constructor-with-no-parameters"></a>Uso di un costruttore senza parametri

Se il DbContext non può essere ottenuto dal provider di servizi dell'applicazione, gli strumenti cercano il `DbContext` tipo derivato all'interno del progetto. Tentano quindi di creare un'istanza usando un costruttore senza parametri. Può essere il costruttore predefinito se `DbContext` è configurato utilizzando il [`OnConfiguring`][7] metodo.

## <a name="from-a-design-time-factory"></a>Da una factory della fase di progettazione

È anche possibile indicare agli strumenti come creare il DbContext implementando l' `IDesignTimeDbContextFactory<TContext>` interfaccia: se una classe che implementa questa interfaccia si trova nello stesso progetto dell'oggetto derivato `DbContext` o nel progetto di avvio dell'applicazione, gli strumenti ignorano le altre modalità di creazione del DbContext e usano invece la factory della fase di progettazione.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> Prima di EFCore 5,0 il `args` parametro non è stato usato (vedere [questo problema][8]).
> Questo problema è stato risolto in EFCore 5,0 e gli eventuali argomenti della fase di progettazione aggiuntivi vengono passati all'applicazione tramite il parametro.

Una factory della fase di progettazione può essere particolarmente utile se è necessario configurare DbContext in modo diverso per la fase di progettazione rispetto al runtime, se il `DbContext` costruttore accetta parametri aggiuntivi non viene registrato in di, se non si usa o se per qualche motivo si preferisce non avere un `BuildWebHost` metodo nella classe dell'applicazione ASP.NET Core `Main` .

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
