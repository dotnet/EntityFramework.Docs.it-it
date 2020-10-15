---
title: EF Core di registrazione
description: Configurazione della registrazione con Entity Framework Core
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063010"
---
# <a name="logging"></a>Registrazione

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) di questo articolo in GitHub.

## <a name="simple-logging"></a>Registrazione semplice

> [!NOTE]
> Questa funzionalità è stata aggiunta in EF Core 5,0.

Entity Framework Core (EF Core) genera messaggi di log per operazioni quali l'esecuzione di una query o il salvataggio di modifiche nel database. È possibile accedervi da qualsiasi tipo di applicazione tramite l'uso di [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> Quando si [configura un'istanza di DbContext](xref:core/miscellaneous/configuring-dbcontext). Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Questo concetto è simile a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Per ulteriori informazioni, vedere [registrazione semplice](xref:core/miscellaneous/events/simple-logging) .

## <a name="aspnet-core-applications"></a>Applicazioni ASP.NET Core

EF Core si integra automaticamente con i meccanismi di registrazione di ASP.NET Core ogni volta che `AddDbContext` `AddDbContextPool` viene utilizzato o. Pertanto, quando si utilizza ASP.NET Core, la registrazione deve essere configurata come descritto nella [documentazione di ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).

## <a name="other-applications"></a>Altre applicazioni

Per la registrazione EF Core è necessario un ILoggerFactory configurato con uno o più provider di registrazione. I provider comuni sono forniti nei pacchetti seguenti:

* [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un semplice logger della console.
* [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): supporta le funzionalità "log di diagnostica" e "flusso di log" dei servizi app Azure.
* [Microsoft. Extensions. Logging. debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): registra in un monitor del debugger usando System. Diagnostics. debug. WriteLine ().
* [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra nel registro eventi di Windows.
* [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): supporta EventSource/EventListener.
* [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra un listener di traccia utilizzando `System.Diagnostics.TraceSource.TraceEvent()` .

Dopo l'installazione dei pacchetti appropriati, l'applicazione deve creare un'istanza singleton/globale di un LoggerFactory. Ad esempio, usando il logger della console:

### <a name="version-3x"></a>[Versione 3. x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Versione 2. x](#tab/v2)

> [!NOTE]
> Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0. È possibile ignorare ed escludere gli avvisi quando si usa 2,2.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

Questa istanza singleton/globale deve quindi essere registrata con EF Core su `DbContextOptionsBuilder` . Ad esempio:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> È molto importante che le applicazioni non creino una nuova istanza di ILoggerFactory per ogni istanza del contesto. Questa operazione comporterà una perdita di memoria e una riduzione delle prestazioni.

## <a name="filtering-what-is-logged"></a>Filtraggio degli elementi registrati

L'applicazione può controllare ciò che viene registrato configurando un filtro in ILoggerProvider. Ad esempio:

### <a name="version-3x"></a>[Versione 3. x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Versione 2. x](#tab/v2)

> [!NOTE]
> Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0. È possibile ignorare ed escludere gli avvisi quando si usa 2,2.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

In questo esempio, il log viene filtrato in modo da restituire solo i messaggi:

* nella categoria ' Microsoft. EntityFrameworkCore. database. Command '
* a livello di "informazioni"

Per EF Core, le categorie di logger sono definite nella `DbLoggerCategory` classe per facilitare la ricerca delle categorie, ma vengono risolte in stringhe semplici.

Per ulteriori informazioni sull'infrastruttura di registrazione sottostante, vedere la [documentazione relativa alla registrazione del ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).
