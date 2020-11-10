---
title: Uso di Microsoft. Extensions. Logging-EF Core
description: Registrazione da EF Core utilizzando Microsoft. Extensions. Logging in ASP.NET Core e altri tipi di applicazioni
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431535"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a>Utilizzo di Microsoft. Extensions. Logging in EF Core

[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) è un meccanismo di registrazione estendibile con provider plug-in per molti sistemi di registrazione comuni. I plug-in forniti da Microsoft (ad esempio [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) e plug-in di terze parti (ad esempio, [Serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sono disponibili come pacchetti NuGet.

Entity Framework Core (EF Core) si integra completamente con `Microsoft.Extensions.Logging` . Tuttavia, si consiglia di usare la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) per un modo più semplice di registrare, in particolare per le applicazioni che non usano l'inserimento delle dipendenze.

## <a name="aspnet-core-applications"></a>Applicazioni ASP.NET Core

`Microsoft.Extensions.Logging` viene [usato per impostazione predefinita nelle applicazioni ASP.NET Core](/aspnet/core/fundamentals/logging). Chiamata di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .

## <a name="other-application-types"></a>Altri tipi di applicazioni

Altri tipi di applicazioni possono usare [GenericHost](/dotnet/core/extensions/generic-host) per ottenere gli stessi modelli di inserimento delle dipendenze usati in ASP.NET Core. È possibile usare AddDbContext o AddDbContextPool come nelle applicazioni ASP.NET Core.

`Microsoft.Extensions.Logging` può essere utilizzato anche per le applicazioni che non utilizzano l'inserimento di dipendenze, sebbene la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) possa essere più semplice da configurare.

`Microsoft.Extensions.Logging` richiede la creazione di un oggetto <xref:Microsoft.Extensions.Logging.LoggerFactory> . Questa factory deve essere archiviata come istanza statica/globale in un punto e usata ogni volta che viene creato un DbContext. Ad esempio, è normale archiviare la factory del logger come proprietà statica in DbContext.

### <a name="ef-core-30-and-above"></a>[EF Core 3,0 e versioni successive](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> In EF Core 2,1, è molto importante che le applicazioni non creino una nuova istanza di LoggerFactory per ogni istanza di DbContext. Questa operazione comporterà una perdita di memoria e una riduzione delle prestazioni. Questo problema è stato risolto in EF Core 3,0 e versioni successive.

***

Questa istanza singleton/globale deve quindi essere registrata con EF Core su <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>Recupero di messaggi dettagliati

> [!TIP]
> Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring. Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.

### <a name="sensitive-data"></a>Dati sensibili

Per impostazione predefinita, EF Core non includerà i valori dei dati nei messaggi di eccezione. Questo è dovuto al fatto che tali dati possono essere riservati e possono essere rivelati nell'uso in produzione se un'eccezione non viene gestita.

Tuttavia, la conoscenza dei valori dei dati, in particolare per le chiavi, può essere molto utile durante il debug. Questa operazione può essere abilitata in EF Core chiamando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Eccezioni di query dettagliate

Per motivi di prestazioni, EF Core non esegue il wrapping di ogni chiamata per leggere un valore dal provider di database in un blocco try-catch. Tuttavia, ciò comporta a volte eccezioni difficili da diagnosticare, specialmente quando il database restituisce un valore NULL quando non è consentito dal modello.

Se <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> si attiva, EF introdurrà i blocchi try-catch e fornirà quindi errori più dettagliati. Esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>Configurazione per messaggi specifici

L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF core consente alle applicazioni di modificare ciò che accade quando viene rilevato un evento specifico. Questa operazione può essere utilizzata per:

* Modificare il livello di registrazione in cui viene registrato l'evento
* Ignora la registrazione del tutto l'evento
* Genera un'eccezione quando si verifica l'evento

### <a name="changing-the-log-level-for-an-event"></a>Modifica del livello di registrazione per un evento

In alcuni casi può essere utile modificare il livello di registrazione predefinito per un evento. Ad esempio, può essere usato per innalzare di livello due eventi aggiuntivi da `LogLevel.Debug` a `LogLevel.Information` :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Annulla la registrazione di un evento

In modo analogo, un singolo evento può essere eliminato dalla registrazione. Questa operazione è particolarmente utile per ignorare un avviso che è stato esaminato e compreso. Esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Genera per un evento

Infine, è possibile configurare EF Core per generare per un determinato evento. Questa operazione è particolarmente utile per la modifica di un avviso in un errore. (In effetti, si tratta dello scopo originale del `ConfigureWarnings` metodo, di conseguenza il nome). Per esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>Filtro e altre configurazioni

Vedere [registrazione in .NET](/dotnet/core/extensions/logging) per informazioni aggiuntive sul filtro dei log e altre configurazioni.

Gli eventi di registrazione EF Core sono definiti in uno dei seguenti:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> per gli eventi comuni a tutti i provider di database EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> per gli eventi comuni a tutti i provider di database relazionali
* Classe simile per gli eventi specifici del provider di database corrente. Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.

Queste definizioni contengono gli ID evento, il livello di log e la categoria per ogni evento, come usato da `Microsoft.Extensions.Logging` .
