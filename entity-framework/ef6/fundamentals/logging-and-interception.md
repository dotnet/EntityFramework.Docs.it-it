---
title: Registrazione e intercettazione delle operazioni di database-EF6
description: Registrazione e intercettazione delle operazioni di database in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: 858e96a781933da52725efa56087bc27db06257d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072668"
---
# <a name="logging-and-intercepting-database-operations"></a>Registrazione e intercettazione delle operazioni di database
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

A partire da Entity Framework 6, in qualsiasi momento Entity Framework invia un comando al database, questo comando può essere intercettato dal codice dell'applicazione. Questa operazione viene usata più di frequente per la registrazione di SQL, ma può essere usata anche per modificare o interrompere il comando.  

In particolare, EF include:  

- Proprietà di log per il contesto simile a DataContext. log in LINQ to SQL  
- Meccanismo per personalizzare il contenuto e la formattazione dell'output inviato al log  
- Blocchi predefiniti di basso livello per l'intercettazione che offrono maggiore controllo/flessibilità  

## <a name="context-log-property"></a>Proprietà log del contesto  

La proprietà DbContext. database. log può essere impostata su un delegato per qualsiasi metodo che accetta una stringa. In genere viene usato con qualsiasi TextWriter impostando il metodo "Write" del TextWriter. Tutti i SQL generati dal contesto corrente verranno registrati nel writer. Il codice seguente, ad esempio, registrerà SQL sulla console:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

Si noti che context. Database. log è impostato su console. Write. Questo è tutto ciò che serve per accedere a SQL alla console.  

È ora possibile aggiungere un semplice codice di query/inserimento/aggiornamento per visualizzare un output:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

Verrà generato l'output seguente:  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

Si noti che si tratta dell'output presumendo che sia già stata eseguita l'inizializzazione del database. Se l'inizializzazione del database non è già stata eseguita, verrà visualizzato molto altro output che Mostra tutte le migrazioni di lavoro eseguite dietro le quinte per verificare o creare un nuovo database.  

## <a name="what-gets-logged"></a>Cosa viene registrato?  

Quando viene impostata la proprietà log, verranno registrate tutte le operazioni seguenti:  

- SQL per tutti i diversi tipi di comandi. Esempio:  
    - Query, incluse le query LINQ normali, le query eSQL e le query non elaborate da metodi come SQLQuery  
    - Inserimenti, aggiornamenti ed eliminazioni generate come parte di SaveChanges  
    - Relazione che carica query come quelle generate dal caricamento lazy  
- Parametri  
- Indica se il comando viene eseguito in modo asincrono  
- Timestamp che indica quando è iniziata l'esecuzione del comando  
- Indica se il comando è stato completato correttamente, non è riuscito generando un'eccezione oppure, per async, è stato annullato  
- Indicazione del valore del risultato  
- Quantità approssimativa di tempo impiegato per eseguire il comando. Si noti che questo è il tempo di invio del comando per ottenere nuovamente l'oggetto risultato. Non è incluso il tempo necessario per leggere i risultati.  

Esaminando l'output di esempio precedente, ognuno dei quattro comandi registrati è:  

- Query risultante dalla chiamata al contesto. Blog. prima  
    - Si noti che il metodo ToString per ottenere SQL non funzionava per questa query perché "First" non fornisce un oggetto IQueryable su cui è possibile chiamare ToString  
- Query risultante dal caricamento lazy del Blog. Post  
    - Si notino i dettagli del parametro per il valore della chiave per cui è in corso il caricamento lazy  
    - Vengono registrate solo le proprietà del parametro impostate sui valori non predefiniti. Ad esempio, la proprietà Size viene visualizzata solo se è diverso da zero.  
- Due comandi risultanti da SaveChangesAsync; uno per l'aggiornamento per modificare un titolo post, l'altro per un inserimento per aggiungere un nuovo post  
    - Si notino i dettagli del parametro per le proprietà FK e title  
    - Si noti che questi comandi vengono eseguiti in modo asincrono  

## <a name="logging-to-different-places"></a>Registrazione in posizioni diverse  

Come illustrato sopra, la registrazione alla console è molto semplice. È anche facile accedere a memoria, file e così via usando diversi tipi di [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).  

Se si ha familiarità con LINQ to SQL si può notare che in LINQ to SQL la proprietà log è impostata sull'oggetto TextWriter effettivo (ad esempio, console. out) mentre in EF la proprietà log è impostata su un metodo che accetta una stringa (ad esempio, console. Write o console. out. Write). Il motivo è quello di separare EF da TextWriter accettando qualsiasi delegato che può fungere da sink per le stringhe. Si supponga, ad esempio, di avere già un Framework di registrazione e di definire un metodo di registrazione come il seguente:  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

Questa operazione può essere collegata alla proprietà del log EF in questo modo:  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>Registrazione risultati  

Il logger predefinito registra il testo del comando (SQL), i parametri e la riga "in esecuzione" con un timestamp prima che il comando venga inviato al database. Una riga "completata" che contiene il tempo trascorso viene registrata dopo l'esecuzione del comando.  

Si noti che per i comandi asincroni la riga "completata" non viene registrata fino a quando l'attività asincrona non viene effettivamente completata, non riesce o viene annullata.  

La riga "completata" contiene informazioni diverse a seconda del tipo di comando e se l'esecuzione ha avuto esito positivo o negativo.  

### <a name="successful-execution"></a>Esecuzione completata  

Per i comandi che vengono completati correttamente, l'output è "completato in x ms con risultato:" seguito da un'indicazione del risultato. Per i comandi che restituiscono un lettore di dati, l'indicazione del risultato è il tipo di [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) restituito. Per i comandi che restituiscono un valore integer, ad esempio il comando Update illustrato sopra, il risultato visualizzato è tale integer.  

### <a name="failed-execution"></a>Esecuzione non riuscita  

Per i comandi che hanno esito negativo generando un'eccezione, l'output contiene il messaggio dell'eccezione. Se ad esempio si usa sqlQuery per eseguire una query su una tabella esistente, l'output del log potrebbe essere simile al seguente:  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>Esecuzione annullata  

Per i comandi asincroni in cui l'attività viene annullata, il risultato potrebbe essere un errore con un'eccezione, poiché si tratta spesso del provider ADO.NET sottostante quando viene eseguito un tentativo di annullamento. Se questa operazione non viene eseguita e l'attività viene annullata in modo corretto, l'output sarà simile al seguente:  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>Modifica del contenuto e della formattazione del log  

In copre la proprietà database. log si avvale di un oggetto DatabaseLogFormatter. Questo oggetto associa efficacemente un'implementazione di IDbCommandInterceptor (vedere di seguito) a un delegato che accetta stringhe e un DbContext. Ciò significa che i metodi su DatabaseLogFormatter vengono chiamati prima e dopo l'esecuzione di comandi da EF. Questi metodi DatabaseLogFormatter raccolgono e formattano l'output del log e lo inviano al delegato.  

### <a name="customizing-databaselogformatter"></a>Personalizzazione di DatabaseLogFormatter  

La modifica degli elementi registrati e della relativa formattazione può essere eseguita creando una nuova classe che deriva da DatabaseLogFormatter ed esegue l'override dei metodi in base alle esigenze. I metodi più comuni per eseguire l'override sono:  

- LogCommand: esegue l'override di questo per modificare il modo in cui vengono registrati i comandi prima che vengano eseguiti. Per impostazione predefinita, LogCommand chiama LogParameter per ogni parametro; è possibile scegliere di eseguire la stessa operazione in sostituzione o di gestire i parametri in modo diverso.  
- LogResult: esegue l'override di questo per modificare il modo in cui viene registrato il risultato dell'esecuzione di un comando.  
- LogParameter: esegue l'override di questo per modificare la formattazione e il contenuto della registrazione dei parametri.  

Si supponga, ad esempio, di voler registrare solo una singola riga prima che ogni comando venga inviato al database. Questa operazione può essere eseguita con due sostituzioni:  

- Eseguire l'override di LogCommand per formattare e scrivere la riga singola di SQL  
- Eseguire l'override di LogResult per non eseguire alcuna operazione.  

Il codice sarà simile al seguente:

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

Per registrare l'output, è sufficiente chiamare il metodo Write che invierà l'output al delegato di scrittura configurato.  

Si noti che questo codice consente di rimuovere semplicisticamente le interruzioni di riga solo come esempio. Probabilmente non funzionerà correttamente per la visualizzazione di SQL complesso.  

### <a name="setting-the-databaselogformatter"></a>Impostazione di DatabaseLogFormatter  

Una volta creata una nuova classe DatabaseLogFormatter, è necessario registrarla con EF. Questa operazione viene eseguita usando la configurazione basata su codice. In breve, ciò significa creare una nuova classe che deriva da DbConfiguration nello stesso assembly della classe DbContext e quindi chiamare SetDatabaseLogFormatter nel costruttore di questa nuova classe. Esempio:  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>Uso del nuovo DatabaseLogFormatter  

Questo nuovo DatabaseLogFormatter verrà ora usato ogni volta che viene impostato database. log. In questo modo, l'esecuzione del codice dalla parte 1 determinerà l'output seguente:  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>Blocchi predefiniti di intercettazione  

Fino a questo punto abbiamo esaminato come usare DbContext. database. log per registrare il database SQL generato da EF. Tuttavia, questo codice è in realtà una facciata relativamente sottile rispetto ad alcuni blocchi predefiniti di basso livello per un'intercettazione più generale.  

### <a name="interception-interfaces"></a>Interfacce di intercettazione  

Il codice di intercettazione è basato sul concetto di interfacce di intercettazione. Queste interfacce ereditano da IDbInterceptor e definiscono metodi che vengono chiamati quando EF esegue un'azione. Lo scopo è quello di avere un'interfaccia per ogni tipo di oggetto intercettato. Ad esempio, l'interfaccia IDbCommandInterceptor definisce i metodi che vengono chiamati prima che EF effettua una chiamata a ExecuteNonQuery, ExecuteScalar, ExecuteReader e ai metodi correlati. Analogamente, l'interfaccia definisce i metodi che vengono chiamati al completamento di ciascuna di queste operazioni. La classe DatabaseLogFormatter esaminata in precedenza implementa questa interfaccia per registrare i comandi.  

### <a name="the-interception-context"></a>Contesto di intercettazione  

Osservando i metodi definiti in una delle interfacce dell'intercettore è evidente che a ogni chiamata viene assegnato un oggetto di tipo DbInterceptionContext o un tipo derivato da questo, ad esempio DbCommandInterceptionContext \<\> . Questo oggetto contiene informazioni contestuali sull'azione che EF sta assumendo. Se, ad esempio, l'azione viene eseguita per conto di un DbContext, il DbContext viene incluso nel DbInterceptionContext. Analogamente, per i comandi eseguiti in modo asincrono, il flag asincrono viene impostato su DbCommandInterceptionContext.  

### <a name="result-handling"></a>Gestione dei risultati  

La \<\> classe DbCommandInterceptionContext contiene le proprietà denominate result, OriginalResult, Exception e originalException. Queste proprietà vengono impostate su null/zero per le chiamate ai metodi di intercettazione che vengono chiamate prima dell'esecuzione dell'operazione, ovvero per... Esecuzione di metodi. Se l'operazione viene eseguita e ha esito positivo, result e OriginalResult vengono impostati sul risultato dell'operazione. Questi valori possono quindi essere osservati nei metodi di intercettazione che vengono chiamati dopo l'esecuzione dell'operazione, ovvero sul... Metodi eseguiti. Analogamente, se l'operazione genera, verranno impostate le proprietà Exception e originalException.  

#### <a name="suppressing-execution"></a>Eliminazione dell'esecuzione  

Se un intercettore imposta la proprietà Result prima dell'esecuzione del comando (in uno dei... Eseguendo metodi), EF non tenterà di eseguire effettivamente il comando, ma userà invece il set di risultati. In altre parole, l'intercettore può impedire l'esecuzione del comando, ma EF continua come se il comando fosse stato eseguito.  

Un esempio di come questa operazione può essere utilizzata è il batch dei comandi che è stato tradizionalmente eseguito con un provider di wrapping. L'intercettore archivia il comando per un'esecuzione successiva come batch, ma "finge" per EF che il comando è stato eseguito normalmente. Si noti che questa operazione richiede più di questo per implementare l'invio in batch, ma questo è un esempio di come è possibile usare la modifica del risultato dell'intercettazione.  

L'esecuzione può anche essere eliminata impostando la proprietà Exception in uno dei... Esecuzione di metodi. Questo fa sì che EF continui come se l'esecuzione dell'operazione non fosse riuscita generando l'eccezione specificata. Questo può, ovviamente, causare l'arresto anomalo dell'applicazione, ma potrebbe anche essere un'eccezione temporanea o un'altra eccezione gestita da EF. Ad esempio, questo può essere usato negli ambienti di test per testare il comportamento di un'applicazione quando l'esecuzione del comando ha esito negativo.  

#### <a name="changing-the-result-after-execution"></a>Modifica del risultato dopo l'esecuzione  

Se un intercettore imposta la proprietà Result dopo l'esecuzione del comando (in uno dei... Metodi eseguiti), EF utilizzerà il risultato modificato anziché il risultato effettivamente restituito dall'operazione. Analogamente, se un intercettore imposta la proprietà Exception dopo l'esecuzione del comando, EF genererà l'eccezione set come se l'operazione avesse generato l'eccezione.  

Un intercettore può inoltre impostare la proprietà Exception su null per indicare che non deve essere generata alcuna eccezione. Questo può essere utile se l'esecuzione dell'operazione non è riuscita, ma l'intercettore desidera che EF continui come se l'operazione avesse avuto esito positivo. Questa operazione comporta in genere anche l'impostazione del risultato, in modo che Entity Framework abbia un valore di risultato da usare come continuerà.  

#### <a name="originalresult-and-originalexception"></a>OriginalResult ed OriginalException  

Dopo l'esecuzione di un'operazione da parte di EF, verranno impostate le proprietà Result e OriginalResult se l'esecuzione non ha esito negativo oppure le proprietà Exception e originalException se l'esecuzione non è riuscita con un'eccezione.  

Le proprietà OriginalResult e originalException sono di sola lettura e vengono impostate solo da EF dopo l'esecuzione effettiva di un'operazione. Queste proprietà non possono essere impostate dagli intercettori. Ciò significa che qualsiasi intercettore può distinguere tra un'eccezione o un risultato che è stato impostato da un altro intercettore anziché l'eccezione reale o il risultato che si è verificato durante l'esecuzione dell'operazione.  

### <a name="registering-interceptors"></a>Registrazione degli intercettori  

Quando una classe che implementa una o più interfacce di intercettazione è stata creata, può essere registrata con EF usando la classe DbInterception. Esempio:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

Gli intercettori possono essere registrati anche a livello di dominio dell'applicazione usando il meccanismo di configurazione basato sul codice DbConfiguration.  

### <a name="example-logging-to-nlog"></a>Esempio: registrazione in NLog  

È necessario riunirle in un esempio che usa IDbCommandInterceptor e [NLog](https://nlog-project.org/) per:  

- Registra un avviso per qualsiasi comando eseguito in modo non asincrono  
- Registra un errore per qualsiasi comando che genera un'eccezione durante l'esecuzione  

Ecco la classe che esegue la registrazione, che deve essere registrata come illustrato in precedenza:  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

Si noti che questo codice usa il contesto di intercettazione per individuare quando un comando viene eseguito in modo non asincrono e per individuare quando si è verificato un errore durante l'esecuzione di un comando.  
