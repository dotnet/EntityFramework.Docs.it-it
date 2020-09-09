---
title: EF Core di registrazione
description: Configurazione della registrazione con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 56d0e3f8c5ecf56ee6bd6ddf0c9668bb8e3ac12f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617749"
---
# <a name="logging"></a>Registrazione

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) di questo articolo in GitHub.

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

``` csharp
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

``` csharp
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
