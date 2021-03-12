---
title: Registrazione semplice-EF Core
description: Registrazione da un EF Core DbContext con LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 24a3de668a0e587dcc0ab2ae5f061b0eb4d4edcf
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024212"
---
# <a name="simple-logging"></a>Registrazione semplice

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

> [!TIP]
> È possibile [scaricare l'esempio di questo articolo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Logging/SimpleLogging) da GitHub.

È possibile utilizzare la registrazione semplice Entity Framework Core (EF Core) per ottenere facilmente i log durante lo sviluppo e il debug di applicazioni. Questa forma di registrazione richiede una configurazione minima e nessun pacchetto NuGet aggiuntivo.

> [!TIP]
> EF Core si integra anche con [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), che richiede una maggiore configurazione, ma è spesso più adatto per la registrazione nelle applicazioni di produzione.

## <a name="configuration"></a>Configurazione

È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'utilizzo di <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> quando si [configura un'istanza di DbContext](xref:core/dbcontext-configuration/index). Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

In alternativa, `LogTo` è possibile chiamare come parte di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o quando si crea un' <xref:Microsoft.EntityFrameworkCore.DbContextOptions> istanza da passare al `DbContext` costruttore.

> [!TIP]
> Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring. Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.

## <a name="directing-the-logs"></a>Indirizzamento dei log

### <a name="logging-to-the-console"></a>Accesso alla console

`LogTo` richiede un <xref:System.Action%601> delegato che accetta una stringa. EF Core chiamerà questo delegato con una stringa per ogni messaggio di log generato. Fino al delegato viene quindi eseguita un'operazione con il messaggio specificato.

Il <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> metodo viene spesso usato per questo delegato, come illustrato in precedenza. In questo modo, ogni messaggio di log viene scritto nella console.

### <a name="logging-to-the-debug-window"></a>Accesso alla finestra di debug

<xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> può essere usato per inviare l'output alla finestra di debug in Visual Studio o in altri IDE. In questo caso è necessario usare la [sintassi lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) perché la `Debug` classe è compilata al di fuori delle build di rilascio. Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a>Registrazione in un file

Per la scrittura in un file è necessario creare un <xref:System.IO.StreamWriter> o simile per il file. Il <xref:System.IO.StreamWriter.WriteLine%2A> metodo può quindi essere usato come negli altri esempi precedenti. Ricordarsi di assicurarsi che il file venga chiuso senza problemi eliminando il writer quando il contesto viene eliminato. Ad esempio:

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true);

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }

    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per la registrazione in file nelle applicazioni di produzione.

## <a name="getting-detailed-messages"></a>Recupero di messaggi dettagliati

### <a name="sensitive-data"></a>Dati sensibili

Per impostazione predefinita, EF Core non includerà i valori dei dati nei messaggi di eccezione. Questo è dovuto al fatto che tali dati possono essere riservati e possono essere rivelati nell'uso in produzione se un'eccezione non viene gestita.

Tuttavia, la conoscenza dei valori dei dati, in particolare per le chiavi, può essere molto utile durante il debug. Questa operazione può essere abilitata in EF Core chiamando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Eccezioni di query dettagliate

Per motivi di prestazioni, EF Core non esegue il wrapping di ogni chiamata per leggere un valore dal provider di database in un blocco try-catch. Tuttavia, ciò comporta a volte eccezioni difficili da diagnosticare, specialmente quando il database restituisce un valore NULL quando non è consentito dal modello.

Se <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> si attiva, EF introdurrà i blocchi try-catch e fornirà quindi errori più dettagliati. Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a>Filtro

### <a name="log-levels"></a>Livelli di registrazione

Ogni messaggio del log EF Core viene assegnato a un livello definito dall' <xref:Microsoft.Extensions.Logging.LogLevel> enumerazione. Per impostazione predefinita, EF Core registrazione semplice include tutti i messaggi a `Debug` livello o superiore. `LogTo` è possibile passare un livello minimo superiore per filtrare alcuni messaggi. Ad esempio, passando `Information` i risultati in un set minimo di log limitato all'accesso al database e ad alcuni messaggi di manutenzione.

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a>Messaggi specifici

A ogni messaggio del log viene assegnato un <xref:Microsoft.Extensions.Logging.EventId> . È possibile accedere a questi ID dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe o dalla <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> classe per i messaggi specifici relazionali. Un provider di database può anche avere ID specifici del provider in una classe simile. Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.

`LogTo` può essere configurato in modo da registrare solo i messaggi associati a uno o più ID evento. Ad esempio, per registrare solo i messaggi per il contesto inizializzato o eliminato:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a>Categorie di messaggi

Ogni messaggio di log viene assegnato a una categoria di logger gerarchici denominata. Le categorie sono:

| Category                                             | Messaggi
|:-----------------------------------------------------|-------------------------------------------------
| Microsoft.EntityFrameworkCore                        | Tutti i messaggi di EF Core
| Microsoft. EntityFrameworkCore. database               | Tutte le interazioni con i database
| Microsoft. EntityFrameworkCore. database. Connection    | Usi di una connessione di database
| Microsoft. EntityFrameworkCore. database. Command       | Usi di un comando di database
| Microsoft. EntityFrameworkCore. database. Transaction   | Utilizzi di una transazione di database
| Microsoft. EntityFrameworkCore. Update                 | Salvataggio di entità, escluse le interazioni tra database
| Microsoft. EntityFrameworkCore. Model                  | Tutte le interazioni tra modelli e metadati
| Microsoft. EntityFrameworkCore. Model. Validation       | Convalida modello
| Microsoft. EntityFrameworkCore. query                  | Query, escluse le interazioni tra database
| Microsoft. EntityFrameworkCore. Infrastructure         | Eventi generali, ad esempio creazione del contesto
| Microsoft. EntityFrameworkCore. impalcatura            | reverse engineering database
| Microsoft. EntityFrameworkCore. Migrations             | Migrazioni
| Microsoft. EntityFrameworkCore. rilevamento modifiche         | Interazioni rilevamento modifiche

`LogTo` può essere configurato in modo da registrare solo i messaggi da una o più categorie. Ad esempio, per registrare solo le interazioni tra database:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

Si noti che la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fornisce un'API gerarchica per trovare una categoria ed evita la necessità di utilizzare stringhe hardcoded.

Poiché le categorie sono gerarchiche, in questo esempio viene utilizzata la `Database` categoria per includere tutti i messaggi per le sottocategorie `Database.Connection` , `Database.Command` e `Database.Transaction` .

### <a name="custom-filters"></a>Filtri personalizzati

`LogTo` consente di usare un filtro personalizzato per i casi in cui nessuna delle opzioni di filtro sopra indicate è sufficiente. Ad esempio, per registrare un messaggio a livello `Information` o superiore, oltre ai messaggi per l'apertura e la chiusura di una connessione:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> Il filtro usando filtri personalizzati o una delle altre opzioni illustrate qui è più efficiente rispetto al filtro nel `LogTo` delegato. Questo perché se il filtro determina che il messaggio non deve essere registrato, il messaggio del log non viene ancora creato.

## <a name="configuration-for-specific-messages"></a>Configurazione per messaggi specifici

L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF core consente alle applicazioni di modificare ciò che accade quando viene rilevato un evento specifico. Questa operazione può essere utilizzata per:

* Modificare il livello di registrazione in cui viene registrato l'evento
* Ignora la registrazione del tutto l'evento
* Genera un'eccezione quando si verifica l'evento

### <a name="changing-the-log-level-for-an-event"></a>Modifica del livello di registrazione per un evento

Nell'esempio precedente è stato usato un filtro personalizzato per registrare ogni messaggio in, oltre a `LogLevel.Information` due eventi definiti per `LogLevel.Debug` . Per ottenere lo stesso risultato, è possibile modificare il livello di registrazione dei due `Debug` eventi in `Information` :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Annulla la registrazione di un evento

In modo analogo, un singolo evento può essere eliminato dalla registrazione. Questa operazione è particolarmente utile per ignorare un avviso che è stato esaminato e compreso. Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Genera per un evento

Infine, è possibile configurare EF Core per generare per un determinato evento. Questa operazione è particolarmente utile per la modifica di un avviso in un errore. (In effetti, si tratta dello scopo originale del `ConfigureWarnings` metodo, di conseguenza il nome). Per esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a>Contenuto e formattazione del messaggio

Il contenuto predefinito da `LogTo` viene formattato su più righe. La prima riga contiene i metadati del messaggio:

* <xref:Microsoft.Extensions.Logging.LogLevel>Come prefisso di quattro caratteri
* Timestamp locale, formattato per le impostazioni cultura correnti
* Oggetto <xref:Microsoft.Extensions.Logging.EventId> nel formato che può essere copiato/incollato per ottenere il membro da <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una delle altre `EventId` classi, più il valore ID non elaborato
* Categoria di eventi, come descritto in precedenza.

Ad esempio:

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

Questo contenuto può essere personalizzato passando i valori da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , come illustrato nelle sezioni seguenti.

> [!TIP]
> Prendere in considerazione l'uso di [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) per un maggiore controllo sulla formattazione dei log.

### <a name="using-utc-time"></a>Utilizzo dell'ora UTC

Per impostazione predefinita, i timestamp sono progettati per l'utilizzo locale durante il debug. Usare <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> invece per usare i timestamp UTC indipendenti dalle impostazioni cultura, ma è possibile conservarne tutti gli altri. Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

Questo esempio genera la seguente formattazione dei log:

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a>Registrazione a riga singola

A volte è utile ottenere esattamente una riga per ogni messaggio di log. Questa operazione può essere abilitata da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

Questo esempio genera la seguente formattazione dei log:

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a>Altre opzioni relative al contenuto

Gli altri flag in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> possono essere utilizzati per ridurre la quantità di metadati inclusi nel log. Questa operazione può essere utile in combinazione con la registrazione a riga singola. Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

Questo esempio genera la seguente formattazione dei log:

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a>Passaggio da EF6

EF Core registrazione semplice differisce da <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in due modi importanti:

* I messaggi di log non sono limitati solo alle interazioni tra database
* La registrazione deve essere configurata in fase di inizializzazione del contesto

Per la prima differenza, è possibile utilizzare il filtro descritto in precedenza per limitare i messaggi che vengono registrati.

La seconda differenza è una modifica intenzionale che consente di migliorare le prestazioni senza generare messaggi di log quando non sono necessari. Tuttavia, è comunque possibile ottenere un comportamento simile a EF6 creando una `Log` Proprietà in `DbContext` e quindi usandola solo quando è stata impostata. Ad esempio:

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
