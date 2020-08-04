---
title: EF Core di registrazione
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 4f39e0ad1f061970aae7f7eb7abdc72e4bb0d691
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526459"
---
# <a name="logging"></a><span data-ttu-id="33008-102">Registrazione</span><span class="sxs-lookup"><span data-stu-id="33008-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="33008-103">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="33008-103">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="33008-104">Applicazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33008-104">ASP.NET Core applications</span></span>

<span data-ttu-id="33008-105">EF Core si integra automaticamente con i meccanismi di registrazione di ASP.NET Core ogni volta che `AddDbContext` `AddDbContextPool` viene utilizzato o.</span><span class="sxs-lookup"><span data-stu-id="33008-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="33008-106">Pertanto, quando si utilizza ASP.NET Core, la registrazione deve essere configurata come descritto nella [documentazione di ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="33008-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="33008-107">Altre applicazioni</span><span class="sxs-lookup"><span data-stu-id="33008-107">Other applications</span></span>

<span data-ttu-id="33008-108">Per la registrazione EF Core è necessario un ILoggerFactory configurato con uno o più provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="33008-108">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="33008-109">I provider comuni sono forniti nei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="33008-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="33008-110">[Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un semplice logger della console.</span><span class="sxs-lookup"><span data-stu-id="33008-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="33008-111">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): supporta le funzionalità "log di diagnostica" e "flusso di log" dei servizi app Azure.</span><span class="sxs-lookup"><span data-stu-id="33008-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="33008-112">[Microsoft. Extensions. Logging. debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): registra in un monitor del debugger usando System. Diagnostics. debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="33008-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="33008-113">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra nel registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="33008-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="33008-114">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): supporta EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="33008-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="33008-115">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra un listener di traccia utilizzando `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="33008-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="33008-116">Dopo l'installazione dei pacchetti appropriati, l'applicazione deve creare un'istanza singleton/globale di un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="33008-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="33008-117">Ad esempio, usando il logger della console:</span><span class="sxs-lookup"><span data-stu-id="33008-117">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="33008-118">Versione 3. x</span><span class="sxs-lookup"><span data-stu-id="33008-118">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="33008-119">Versione 2. x</span><span class="sxs-lookup"><span data-stu-id="33008-119">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="33008-120">Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0.</span><span class="sxs-lookup"><span data-stu-id="33008-120">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="33008-121">È possibile ignorare ed escludere gli avvisi quando si usa 2,2.</span><span class="sxs-lookup"><span data-stu-id="33008-121">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="33008-122">Questa istanza singleton/globale deve quindi essere registrata con EF Core su `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="33008-122">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="33008-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="33008-123">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="33008-124">È molto importante che le applicazioni non creino una nuova istanza di ILoggerFactory per ogni istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="33008-124">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="33008-125">Questa operazione comporterà una perdita di memoria e una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="33008-125">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="33008-126">Filtraggio degli elementi registrati</span><span class="sxs-lookup"><span data-stu-id="33008-126">Filtering what is logged</span></span>

<span data-ttu-id="33008-127">L'applicazione può controllare ciò che viene registrato configurando un filtro in ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="33008-127">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="33008-128">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="33008-128">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="33008-129">Versione 3. x</span><span class="sxs-lookup"><span data-stu-id="33008-129">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="33008-130">Versione 2. x</span><span class="sxs-lookup"><span data-stu-id="33008-130">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="33008-131">Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0.</span><span class="sxs-lookup"><span data-stu-id="33008-131">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="33008-132">È possibile ignorare ed escludere gli avvisi quando si usa 2,2.</span><span class="sxs-lookup"><span data-stu-id="33008-132">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="33008-133">In questo esempio, il log viene filtrato in modo da restituire solo i messaggi:</span><span class="sxs-lookup"><span data-stu-id="33008-133">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="33008-134">nella categoria ' Microsoft. EntityFrameworkCore. database. Command '</span><span class="sxs-lookup"><span data-stu-id="33008-134">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="33008-135">a livello di "informazioni"</span><span class="sxs-lookup"><span data-stu-id="33008-135">at the 'Information' level</span></span>

<span data-ttu-id="33008-136">Per EF Core, le categorie di logger sono definite nella `DbLoggerCategory` classe per facilitare la ricerca delle categorie, ma vengono risolte in stringhe semplici.</span><span class="sxs-lookup"><span data-stu-id="33008-136">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="33008-137">Per ulteriori informazioni sull'infrastruttura di registrazione sottostante, vedere la [documentazione relativa alla registrazione del ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="33008-137">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
