---
title: Migrazioni con più provider-EF Core
description: Utilizzo delle migrazioni per gestire gli schemi di database quando sono destinati a più provider di database con Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: feed19abb188eebc473386b67fac62848e682d96
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024095"
---
# <a name="migrations-with-multiple-providers"></a>Migrazioni con più provider

Gli [strumenti di EF Core](xref:core/cli/index) solo le migrazioni con impalcature per il provider attivo. In alcuni casi, tuttavia, potrebbe essere necessario usare più di un provider (ad esempio Microsoft SQL Server e SQLite) con la DbContext. Gestire questo problema gestendo più set di migrazioni, uno per ogni provider, e aggiungendo una migrazione a ogni modifica del modello.

## <a name="using-multiple-context-types"></a>Uso di più tipi di contesto

Un modo per creare più set di migrazioni consiste nell'usare un tipo DbContext per provider.

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

Specificare il tipo di contesto quando si aggiungono nuove migrazioni.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Non è necessario specificare la directory di output per le successive migrazioni perché vengono create come elementi di pari livello rispetto all'ultima.

## <a name="using-one-context-type"></a>Uso di un tipo di contesto

È anche possibile usare un tipo DbContext. Questa operazione richiede attualmente lo spostamento delle migrazioni in un assembly separato. Per istruzioni sulla configurazione dei progetti, vedere [utilizzo di un progetto di migrazioni separate](xref:core/managing-schemas/migrations/projects) .

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations) di questo articolo in GitHub.

A partire da EF Core 5,0, è possibile passare argomenti nell'app dagli strumenti. Questo può consentire un flusso di lavoro più semplificato che evita di dover apportare modifiche manuali al progetto durante l'esecuzione degli strumenti.

Di seguito è riportato un modello che funziona bene quando si usa un [host generico](/dotnet/core/extensions/generic-host).

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

Poiché il generatore host predefinito legge la configurazione dagli argomenti della riga di comando, è possibile specificare il provider durante l'esecuzione degli strumenti.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> Il `--` token indirizza `dotnet ef` a trattare tutti gli elementi che seguono come argomento e non tenta di analizzarli come opzioni. Qualsiasi argomento aggiuntivo non usato da `dotnet ef` viene inviato all'app.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> La possibilità di specificare argomenti aggiuntivi per l'app è stata aggiunta in EF Core 5,0. Se si usa una versione precedente, specificare invece i valori di configurazione con le variabili di ambiente.
