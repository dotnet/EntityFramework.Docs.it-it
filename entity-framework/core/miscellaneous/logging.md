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
# <a name="logging"></a><span data-ttu-id="f5126-103">Registrazione</span><span class="sxs-lookup"><span data-stu-id="f5126-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="f5126-104">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="f5126-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="f5126-105">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="f5126-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="f5126-106">Questa funzionalità è stata aggiunta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f5126-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="f5126-107">Entity Framework Core (EF Core) genera messaggi di log per operazioni quali l'esecuzione di una query o il salvataggio di modifiche nel database.</span><span class="sxs-lookup"><span data-stu-id="f5126-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="f5126-108">È possibile accedervi da qualsiasi tipo di applicazione tramite l'uso di [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="f5126-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="f5126-109">Quando si [configura un'istanza di DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f5126-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="f5126-110">Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f5126-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f5126-111">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f5126-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="f5126-112">Questo concetto è simile a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span><span class="sxs-lookup"><span data-stu-id="f5126-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="f5126-113">Per ulteriori informazioni, vedere [registrazione semplice](xref:core/miscellaneous/events/simple-logging) .</span><span class="sxs-lookup"><span data-stu-id="f5126-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="f5126-114">Applicazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5126-114">ASP.NET Core applications</span></span>

<span data-ttu-id="f5126-115">EF Core si integra automaticamente con i meccanismi di registrazione di ASP.NET Core ogni volta che `AddDbContext` `AddDbContextPool` viene utilizzato o.</span><span class="sxs-lookup"><span data-stu-id="f5126-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="f5126-116">Pertanto, quando si utilizza ASP.NET Core, la registrazione deve essere configurata come descritto nella [documentazione di ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="f5126-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="f5126-117">Altre applicazioni</span><span class="sxs-lookup"><span data-stu-id="f5126-117">Other applications</span></span>

<span data-ttu-id="f5126-118">Per la registrazione EF Core è necessario un ILoggerFactory configurato con uno o più provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="f5126-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="f5126-119">I provider comuni sono forniti nei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f5126-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="f5126-120">[Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un semplice logger della console.</span><span class="sxs-lookup"><span data-stu-id="f5126-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="f5126-121">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): supporta le funzionalità "log di diagnostica" e "flusso di log" dei servizi app Azure.</span><span class="sxs-lookup"><span data-stu-id="f5126-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="f5126-122">[Microsoft. Extensions. Logging. debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): registra in un monitor del debugger usando System. Diagnostics. debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="f5126-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="f5126-123">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra nel registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="f5126-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="f5126-124">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): supporta EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="f5126-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="f5126-125">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra un listener di traccia utilizzando `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="f5126-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="f5126-126">Dopo l'installazione dei pacchetti appropriati, l'applicazione deve creare un'istanza singleton/globale di un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="f5126-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="f5126-127">Ad esempio, usando il logger della console:</span><span class="sxs-lookup"><span data-stu-id="f5126-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="f5126-128">Versione 3. x</span><span class="sxs-lookup"><span data-stu-id="f5126-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="f5126-129">Versione 2. x</span><span class="sxs-lookup"><span data-stu-id="f5126-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="f5126-130">Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0.</span><span class="sxs-lookup"><span data-stu-id="f5126-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="f5126-131">È possibile ignorare ed escludere gli avvisi quando si usa 2,2.</span><span class="sxs-lookup"><span data-stu-id="f5126-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="f5126-132">Questa istanza singleton/globale deve quindi essere registrata con EF Core su `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f5126-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="f5126-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f5126-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="f5126-134">È molto importante che le applicazioni non creino una nuova istanza di ILoggerFactory per ogni istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="f5126-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="f5126-135">Questa operazione comporterà una perdita di memoria e una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f5126-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="f5126-136">Filtraggio degli elementi registrati</span><span class="sxs-lookup"><span data-stu-id="f5126-136">Filtering what is logged</span></span>

<span data-ttu-id="f5126-137">L'applicazione può controllare ciò che viene registrato configurando un filtro in ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="f5126-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="f5126-138">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f5126-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="f5126-139">Versione 3. x</span><span class="sxs-lookup"><span data-stu-id="f5126-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="f5126-140">Versione 2. x</span><span class="sxs-lookup"><span data-stu-id="f5126-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="f5126-141">Nell'esempio di codice seguente viene usato un `ConsoleLoggerProvider` Costruttore obsoleto nella versione 2,2 e sostituito in 3,0.</span><span class="sxs-lookup"><span data-stu-id="f5126-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="f5126-142">È possibile ignorare ed escludere gli avvisi quando si usa 2,2.</span><span class="sxs-lookup"><span data-stu-id="f5126-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="f5126-143">In questo esempio, il log viene filtrato in modo da restituire solo i messaggi:</span><span class="sxs-lookup"><span data-stu-id="f5126-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="f5126-144">nella categoria ' Microsoft. EntityFrameworkCore. database. Command '</span><span class="sxs-lookup"><span data-stu-id="f5126-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="f5126-145">a livello di "informazioni"</span><span class="sxs-lookup"><span data-stu-id="f5126-145">at the 'Information' level</span></span>

<span data-ttu-id="f5126-146">Per EF Core, le categorie di logger sono definite nella `DbLoggerCategory` classe per facilitare la ricerca delle categorie, ma vengono risolte in stringhe semplici.</span><span class="sxs-lookup"><span data-stu-id="f5126-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="f5126-147">Per ulteriori informazioni sull'infrastruttura di registrazione sottostante, vedere la [documentazione relativa alla registrazione del ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="f5126-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
