---
title: Intercettori-EF Core
description: Intercettazione per operazioni di database e altri eventi
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: fba9f3d02b8cf504c2cadca8eb844cd3e818e915
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635809"
---
# <a name="interceptors"></a>Intercettori

Gli intercettori Entity Framework Core (EF Core) consentono l'intercettazione, la modifica e/o l'eliminazione di EF Core operazioni. Sono incluse operazioni di database di basso livello, ad esempio l'esecuzione di un comando, nonché operazioni di livello superiore, ad esempio chiamate a SaveChanges.

Gli intercettori sono diversi dalla registrazione e dalla diagnostica in quanto consentono la modifica o l'eliminazione dell'operazione intercettata. La registrazione [semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) è una scelta migliore per la registrazione.

Gli intercettori vengono registrati per ogni istanza di DbContext al momento della configurazione del contesto. Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.

## <a name="registering-interceptors"></a>Registrazione degli intercettori

Gli intercettori vengono registrati utilizzando <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> quando si [configura un'istanza di DbContext](xref:core/dbcontext-configuration/index). Questa operazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Ad esempio:

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

In alternativa, `AddInterceptors` è possibile chiamare come parte di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o quando si crea un' <xref:Microsoft.EntityFrameworkCore.DbContextOptions> istanza da passare al costruttore DbContext.

> [!TIP]
> Quando si usa AddDbContext o viene passata un'istanza di DbContextOptions al costruttore DbContext, viene comunque chiamato Configuring. Questo lo rende la posizione ideale per applicare la configurazione del contesto indipendentemente dalla costruzione del DbContext.

Gli intercettori spesso sono senza stato, il che significa che è possibile usare una singola istanza dell'intercettore per tutte le istanze di DbContext. Ad esempio:

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor 
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

Ogni istanza dell'intercettore deve implementare una o più interfacce derivate da <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> . Ogni istanza deve essere registrata solo una volta, anche se implementa più interfacce di intercettazione. EF Core instraderà gli eventi per ogni interfaccia nel modo appropriato.

## <a name="database-interception"></a>Intercettazione database

> [!NOTE]
> L'intercettazione del database è stata introdotta in EF Core 3,0 ed è disponibile solo per i provider di database relazionali.
> Il supporto di salvataggio è stato introdotto in EF Core 5,0.

L'intercettazione di database di basso livello è suddivisa nelle tre interfacce illustrate nella tabella seguente.

| Intercettore                                                            | Operazioni di database intercettate
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | Creazione di comandi</br>Esecuzione di comandi</br>Errori di comando</br>Eliminazione della finestra DbDataReader del comando
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | Apertura e chiusura delle connessioni</br>Errori di connessione
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | Creazione di transazioni</br>Utilizzo di transazioni esistenti</br>Commit di transazioni</br>Rollback di transazioni</br>Creazione e uso di salvataggio</br>Errori delle transazioni

Le classi base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> e <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contengono implementazioni senza op per ogni metodo nell'interfaccia corrispondente. Utilizzare le classi base per evitare la necessità di implementare metodi di intercettazione non utilizzati.

I metodi di ogni tipo di intercettore sono in coppia, con la prima chiamata prima che l'operazione di database venga avviata e la seconda dopo il completamento dell'operazione. Ad esempio, Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> viene chiamato prima dell'esecuzione di una query e <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> viene chiamato dopo che la query è stata inviata al database.

Ogni coppia di metodi ha sia la sincronizzazione che le variazioni asincrone. Ciò consente l'esecuzione di operazioni di I/O asincrone, ad esempio la richiesta di un token di accesso, come parte dell'intercettazione di un'operazione asincrona sul database.

### <a name="example-command-interception-to-add-query-hints"></a>Esempio: intercettazione di comandi per aggiungere hint per la query

> [!TIP]  
> È possibile [scaricare l'esempio Command Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) da GitHub.

Un oggetto <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> può essere utilizzato per modificare SQL prima di essere inviato al database. Questo esempio illustra come modificare SQL per includere un hint per la query.

Spesso la parte trickiest dell'intercettazione sta determinando quando il comando corrisponde alla query che deve essere modificata. L'analisi di SQL è un'opzione, ma tende a essere fragile. Un'altra opzione consiste nell'usare [EF Core tag di query](xref:core/querying/tags) per contrassegnare ogni query che deve essere modificata. Ad esempio:

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

Questo tag può quindi essere rilevato nell'intercettore perché sarà sempre incluso come commento nella prima riga del testo del comando. Quando si rileva il tag, la query SQL viene modificata per aggiungere l'hint appropriato:

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

Notare:

* L'intercettore eredita da <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> per evitare di dover implementare ogni metodo nell'interfaccia dell'intercettore.
* L'intercettore implementa entrambi i metodi Sync e Async. In questo modo si garantisce che lo stesso hint per la query venga applicato alle query asincrone e di sincronizzazione.
* L'intercettore implementa i `Executing` metodi che vengono chiamati da EF core con l'oggetto SQL generato _prima_ che venga inviato al database. Si confronti con i `Executed` metodi, che vengono chiamati dopo che la chiamata al database ha restituito.

Quando si esegue il tag di una query, l'esecuzione del codice in questo esempio genera quanto segue:

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

D'altra parte, quando una query non è contrassegnata, viene inviata al database senza modifiche:

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>Esempio: intercettazione della connessione per l'autenticazione SQL Azure usando Aggiungi

> [!TIP]  
> È possibile [scaricare l'esempio Connection Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) da GitHub.

Un oggetto <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> può essere utilizzato per modificare l'oggetto <xref:System.Data.Common.DbConnection> prima che venga utilizzato per connettersi al database. Questa operazione può essere usata per ottenere un token di accesso Azure Active Directory (AAD). Ad esempio:

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) supporta ora l'autenticazione AAD tramite la stringa di connessione. Per altre informazioni, vedere <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod>.

> [!WARNING]
> Si noti che l'intercettore genera un'eccezione se viene eseguita una chiamata di sincronizzazione per aprire la connessione. Ciò è dovuto al fatto che non è disponibile alcun metodo asincrono per ottenere il token di accesso e non esiste [alcun modo universale e semplice per chiamare un metodo asincrono da un contesto non asincrono senza rischiare il deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).

> [!WARNING]
> in alcune situazioni il token di accesso potrebbe non essere memorizzato automaticamente nella cache del provider di token di Azure. A seconda del tipo di token richiesto, potrebbe essere necessario implementare la memorizzazione nella cache.

### <a name="example-advanced-command-interception-for-caching"></a>Esempio: intercettazione dei comandi avanzati per la memorizzazione nella cache

> [!TIP]  
> È possibile [scaricare l'esempio Advanced Command Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) da GitHub.

Gli intercettori di EF Core possono:

* Indica EF Core di non visualizzare l'esecuzione dell'operazione intercettata
* Modificare il risultato dell'operazione restituito a EF Core

Questo esempio mostra un intercettore che usa queste funzionalità per comportarsi come una cache di secondo livello primitiva. I risultati della query memorizzati nella cache vengono restituiti per una query specifica, evitando un round trip del database.

> [!WARNING]
> Prestare attenzione quando si modifica il comportamento predefinito EF Core in questo modo. EF Core possono comportarsi in modo imprevisto se si ottiene un risultato anomalo che non può essere elaborato correttamente. Inoltre, in questo esempio vengono illustrati i concetti dell'intercettore; non è concepito come modello per un'implementazione affidabile della cache di secondo livello.

In questo esempio, l'applicazione esegue spesso una query per ottenere il messaggio "Daily Message" più recente:

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

Questa query viene [contrassegnata](xref:core/querying/tags) in modo da poter essere facilmente rilevata nell'intercettore. L'idea è eseguire solo una query sul database per un nuovo messaggio una volta al giorno. In altri casi, l'applicazione utilizzerà un risultato memorizzato nella cache. (Nell'esempio viene utilizzato un ritardo di 10 secondi nell'esempio per simulare un nuovo giorno).

#### <a name="interceptor-state"></a>Stato intercettore

Questo intercettore è con stato: archivia l'ID e il testo del messaggio del messaggio giornaliero più recente sottoposto a query, più l'ora in cui è stata eseguita la query. A causa di questo stato, è necessario anche un [blocco](/dotnet/csharp/language-reference/keywords/lock-statement) poiché la memorizzazione nella cache richiede che lo stesso intercettore venga utilizzato da più istanze di contesto.

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>Prima dell'esecuzione

Nel `Executing` Metodo (ad esempio prima di effettuare una chiamata al database), l'intercettore rileva la query con tag, quindi controlla se è presente un risultato memorizzato nella cache. Se viene trovato un risultato di questo tipo, la query viene annullata e vengono invece usati i risultati memorizzati nella cache.

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

Si noti il modo in cui il codice chiama <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> e passa una sostituzione <xref:System.Data.Common.DbDataReader> contenente i dati memorizzati nella cache. Viene quindi restituito questo InterceptionResult, causando l'eliminazione dell'esecuzione della query. Il lettore di sostituzione viene invece utilizzato da EF Core come risultato della query.

Questo intercettore modifica anche il testo del comando. Questa manipolazione non è necessaria, ma migliora la chiarezza nei messaggi di log. Non è necessario che il testo del comando sia SQL valido perché la query non verrà eseguita.

#### <a name="after-execution"></a>Dopo l'esecuzione

Se non è disponibile alcun messaggio memorizzato nella cache o se è scaduto, il codice precedente non elimina il risultato. EF Core eseguirà quindi la query normalmente. Viene quindi restituito al metodo dell'intercettore `Executed` dopo l'esecuzione. A questo punto, se il risultato non è già un reader memorizzato nella cache, il nuovo ID messaggio e la stringa vengono esatti dal lettore reale e memorizzati nella cache pronti per l'uso successivo di questa query.

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a>Dimostrazione

L' [esempio di intercettore Caching](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contiene una semplice applicazione console che esegue query per i messaggi giornalieri per testare la memorizzazione nella cache:

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }
        
        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

Si ottiene l'output seguente:

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

Si noti che dall'output del log l'applicazione continua a usare il messaggio memorizzato nella cache fino alla scadenza del timeout, a quel punto viene eseguita nuovamente una query sul database per eventuali nuovi messaggi.

## <a name="savechanges-interception"></a>Intercettazione SaveChanges

> [!NOTE]
> L'intercettazione di SaveChanges è stata introdotta in EF Core 5,0.

> [!TIP]  
> È possibile [scaricare l'esempio di intercettore SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) da GitHub.

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>i punti di intercettazione e sono definiti dall' <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interfaccia. Come per gli altri intercettori, la <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> classe base con metodi no-op viene fornita come praticità.

> [!TIP]
> Gli intercettori sono potenti. In molti casi, tuttavia, potrebbe essere più semplice eseguire l'override del metodo SaveChanges o usare gli [eventi .NET per SaveChanges](xref:core/logging-events-diagnostics/events) esposto in DbContext.

### <a name="example-savechanges-interception-for-auditing"></a>Esempio: intercettazione SaveChanges per il controllo

È possibile intercettare SaveChanges per creare un record di controllo indipendente delle modifiche apportate.

> [!NOTE]
> Non si tratta di una soluzione di controllo affidabile. Piuttosto, si tratta di un esempio semplicistico utilizzato per illustrare le funzionalità di intercettazione.

#### <a name="the-application-context"></a>Contesto dell'applicazione

L' [esempio per il controllo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) usa un DbContext semplice con Blog e post.

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

Si noti che una nuova istanza dell'intercettore è registrata per ogni istanza di DbContext. Questo perché l'intercettore di controllo contiene lo stato collegato all'istanza del contesto corrente.

#### <a name="the-audit-context"></a>Contesto di controllo

L'esempio contiene anche un secondo DbContext e un modello utilizzato per il database di controllo.

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a>Intercettore

L'idea generale del controllo con l'intercettore è la seguente:

* Un messaggio di controllo viene creato all'inizio di SaveChanges e viene scritto nel database di controllo
* SaveChanges può continuare
* Se SaveChanges ha esito positivo, il messaggio di controllo viene aggiornato per indicare l'esito positivo
* Se SaveChanges ha esito negativo, il messaggio di controllo viene aggiornato per indicare l'errore

La prima fase viene gestita prima che tutte le modifiche vengano inviate al database usando le sostituzioni di <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> .

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

L'override di entrambi i metodi Sync e async garantisce che il controllo avvenga indipendentemente dal fatto che `SaveChanges` `SaveChangesAsync` venga chiamato o. Si noti inoltre che l'overload asincrono è in grado di eseguire operazioni di I/O asincrone non bloccanti nel database di controllo. È possibile che si desideri generare il `SavingChanges` metodo di sincronizzazione per assicurarsi che tutte le operazioni di i/O del database siano asincrone. Quindi richiede che l'applicazione chiami sempre `SaveChangesAsync` e mai `SaveChanges` .

#### <a name="the-audit-message"></a>Messaggio di controllo

Ogni metodo dell'intercettore dispone di un `eventData` parametro che fornisce informazioni contestuali sull'evento intercettato. In questo caso l'applicazione corrente DbContext viene inclusa nei dati dell'evento, che viene quindi utilizzata per creare un messaggio di controllo.

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

Il risultato è un' `SaveChangesAudit` entità con una raccolta di `EntityAudit` entità, una per ogni istruzione INSERT, Update o DELETE. L'intercettore inserisce quindi queste entità nel database di controllo.

> [!TIP]
> Viene eseguito l'override di ToString in ogni classe di dati di evento EF Core per generare il messaggio di log equivalente per l'evento. Ad esempio, la chiamata di `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inizializzato" BlogsContext "utilizzando il provider" Microsoft. EntityFrameworkCore. sqlite "con options: None".

#### <a name="detecting-success"></a>Rilevamento dell'esito positivo

L'entità di controllo è archiviata nell'intercettore, in modo che sia possibile accedervi nuovamente quando SaveChanges ha esito positivo o negativo. Per l'esito positivo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> viene chiamato o.

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

L'entità di controllo è collegata al contesto di controllo, poiché esiste già nel database e deve essere aggiornata. Vengono quindi impostati `Succeeded` e `EndTime` , che contrassegnano queste proprietà come modificate in modo che SaveChanges invii un aggiornamento al database di controllo.

#### <a name="detecting-failure"></a>Rilevamento di errori

L'errore viene gestito in modo analogo all'esito positivo, ma <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> nel <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> metodo o. I dati dell'evento contengono l'eccezione generata.

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a>Dimostrazione

L' [esempio di controllo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contiene una semplice applicazione console che apporta modifiche al database blogging e quindi Mostra il controllo creato.

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

Il risultato Mostra il contenuto del database di controllo:

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
