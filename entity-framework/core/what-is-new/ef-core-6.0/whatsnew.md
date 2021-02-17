---
title: Novità di EF Core 6,0
description: Panoramica delle nuove funzionalità di EF Core 6,0
author: ajcvickers
ms.date: 01/28/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: bcc2b3ce9047a2c6b5a89e99b96919914bcf42fe
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543198"
---
# <a name="whats-new-in-ef-core-60"></a>Novità di EF Core 6,0

EF Core 6,0 è attualmente in fase di sviluppo. Questo articolo contiene una panoramica delle modifiche interessanti introdotte in ogni anteprima.

Questa pagina non duplica il [piano per EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan). Il piano descrive i temi generali per EF Core 6,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.

## <a name="ef-core-60-preview-1"></a>EF Core 6,0 Preview 1

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutti gli esempi di anteprima 1 mostrati di seguito [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).

### <a name="unicodeattribute"></a>UnicodeAttribute

Problema di GitHub: [#19794](https://github.com/dotnet/efcore/issues/19794). Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .

A partire da EF Core 6,0, è ora possibile eseguire il mapping di una proprietà di stringa a una colonna non Unicode usando un attributo di mapping _senza specificare direttamente il tipo di database_. Si consideri, ad esempio, un `Book` tipo di entità con una proprietà per il [numero di libro standard internazionale (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) nel formato "ISBN 978-3-16-148410-0":

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

Poiché i codici ISBN non possono contenere caratteri non Unicode, l' `Unicode` attributo provocherà l'uso di un tipo stringa non Unicode. Inoltre, `MaxLength` viene utilizzato per limitare le dimensioni della colonna di database. Quando si utilizza SQL Server, ad esempio, viene generata una colonna del database `varchar(22)` :

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> EF Core esegue il mapping delle proprietà di stringa alle colonne Unicode per impostazione predefinita. `UnicodeAttribute` viene ignorato quando il sistema del database supporta solo tipi Unicode.

### <a name="precisionattribute"></a>PrecisionAttribute

Problema di GitHub: [#17914](https://github.com/dotnet/efcore/issues/17914). Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .

È ora possibile configurare la precisione e la scala di una colonna di database usando gli attributi di mapping _senza specificare direttamente il tipo di database_. Si consideri, ad esempio, un `Product` tipo di entità con una `Price` proprietà Decimal:

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

EF Core eseguirà il mapping di questa proprietà a una colonna del database con precisione 10 e scala 2. Ad esempio, in SQL Server:

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a>EntityTypeConfigurationAttribute

Problema di GitHub: [#23163](https://github.com/dotnet/efcore/issues/23163). Questa funzionalità è stata aggiunta come contributo da [@KaloyanIT](https://github.com/KaloyanIT) .

<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> le istanze consentono <xref:Microsoft.EntityFrameworkCore.ModelBuilder> la configurazione di ogni tipo di entità in modo che sia contenuta nella propria classe di configurazione. Ad esempio:

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

In genere, è necessario creare un'istanza di questa classe di configurazione e chiamarla da <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Ad esempio:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

A partire da EF Core 6,0, un `EntityTypeConfigurationAttribute` può essere inserito nel tipo di entità in modo che EF core possa trovare e usare la configurazione appropriata. Ad esempio:

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

Questo attributo indica che EF Core utilizzerà l' `IEntityTypeConfiguration` implementazione specificata ogni volta che il `Book` tipo di entità viene incluso in un modello. Il tipo di entità è incluso in un modello utilizzando uno dei meccanismi normali. Ad esempio, creando una <xref:Microsoft.EntityFrameworkCore.DbSet%601> proprietà per il tipo di entità:

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

In alternativa, registrarlo in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> `EntityTypeConfigurationAttribute` i tipi non verranno individuati automaticamente in un assembly. I tipi di entità devono essere aggiunti al modello prima che l'attributo venga individuato in tale tipo di entità.

### <a name="translate-tostring-on-sqlite"></a>Translate ToString in SQLite

Problema di GitHub: [#17223](https://github.com/dotnet/efcore/issues/17223). Questa funzionalità è stata aggiunta come contributo da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .

Le chiamate a <xref:System.Object.ToString> vengono ora convertite in SQL quando si usa il provider di database SQLite. Questa operazione può essere utile per le ricerche di testo che coinvolgono colonne non di stringa. Si consideri, ad esempio, un `User` tipo di entità che archivia i numeri di telefono come valori numerici:

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

`ToString` può essere usato per convertire il numero in una stringa nel database. È quindi possibile usare questa stringa con una funzione come `LIKE` per trovare i numeri che corrispondono a un modello. Ad esempio, per trovare tutti i numeri contenenti 555:

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

Questa operazione viene convertita nel codice SQL seguente quando si usa un database SQLite:

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

Si noti che la traduzione di <xref:System.Object.ToString> per SQL Server è già supportata in EF Core 5,0 e può essere supportata anche da altri provider di database.

### <a name="effunctionsrandom"></a>EF. Functions. Random

Problema di GitHub: [#16141](https://github.com/dotnet/efcore/issues/16141). Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .

`EF.Functions.Random` esegue il mapping a una funzione di database che restituisce un numero pseudo-casuale compreso tra 0 e 1 esclusivo. Le traduzioni sono state implementate nel repository EF Core per SQL Server, SQLite e Cosmos. Si consideri, ad esempio, un `User` tipo di entità con una `Popularity` proprietà:

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

`Popularity` i valori possono essere compresi tra 1 e 5. Utilizzando `EF.Functions.Random` è possibile scrivere una query per restituire tutti gli utenti con una popolarità scelta in modo casuale:

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

In questo modo viene convertito nel codice SQL seguente quando si utilizza un database di SQL Server:

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a>Supporto per SQL Server colonne di tipo sparse

Problema di GitHub: [#8023](https://github.com/dotnet/efcore/issues/8023).

SQL Server [colonne di tipo sparse](/sql/relational-databases/tables/use-sparse-columns) sono colonne ordinarie ottimizzate per l'archiviazione di valori null. Questa operazione può essere utile quando si utilizza il [mapping di ereditarietà TPH](xref:core/modeling/inheritance) in cui le proprietà di un sottotipo raramente utilizzato determineranno valori di colonna null per la maggior parte delle righe della tabella. Si consideri, ad esempio, una `ForumModerator` classe che si estende da `ForumUser` :

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

Potrebbero essere presenti milioni di utenti, solo alcuni dei quali sono moderatori. Questo significa che il mapping `ForumName` di come sparse potrebbe avere senso qui. A questo punto è possibile configurare usando `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> . Ad esempio:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

EF Core le migrazioni contrassegnano quindi la colonna come sparse. Ad esempio:

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> Le colonne di tipo sparse presentano limitazioni. Assicurarsi di leggere la [documentazione relativa alle colonne di tipo sparse SQL Server](/sql/relational-databases/tables/use-sparse-columns) per assicurarsi che le colonne di tipo sparse siano la scelta migliore per lo scenario.

### <a name="in-memory-database-validate-required-properties-are-not-null"></a>Database in memoria: le proprietà richieste Validate non sono null

Problema di GitHub: [#10613](https://github.com/dotnet/efcore/issues/10613). Questa funzionalità è stata aggiunta come contributo da [@fagnercarvalho](https://github.com/fagnercarvalho) .

Il EF Core database in memoria genera ora un'eccezione se viene effettuato un tentativo di salvare un valore null per una proprietà contrassegnata come obbligatoria. Si consideri, ad esempio, un `User` tipo con una `Username` proprietà obbligatoria:

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

Se si tenta di salvare un'entità con un valore null `Username` , verrà generata l'eccezione seguente:

> Microsoft. EntityFrameworkCore. eccezione dbupdateexception: mancano le proprietà obbligatorie ' {' username '}' per l'istanza del tipo di entità' User ' con il valore di chiave ' {ID: 1}'.

Questa convalida può essere disabilitata, se necessario. Ad esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a>Traduzione SQL Server migliorata per IsNullOrWhitespace

Problema di GitHub: [#22916](https://github.com/dotnet/efcore/issues/22916). Questa funzionalità è stata aggiunta come contributo da [@Marusyk](https://github.com/Marusyk) .

Si consideri la query seguente:

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

Prima di EF Core 6,0, questa operazione è stata convertita nel SQL Server seguente:

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

Questa traduzione è stata migliorata per EF Core 6,0 a:

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a>I commenti del database sono con ponteggi ai commenti del codice

Problema di GitHub: [#19113](https://github.com/dotnet/efcore/issues/19113). Questa funzionalità è stata aggiunta come contributo da [@ErikEJ](https://github.com/ErikEJ) .

I commenti sulle tabelle e sulle colonne SQL sono ora sottoposte a impalcatura nei tipi di entità creati durante la [decompilazione di un modello di EF Core](xref:core/managing-schemas/scaffolding) da un database SQL Server esistente. Ad esempio:

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a>Microsoft. Data. sqlite 6,0 Preview 1

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutti gli esempi di anteprima 1 mostrati di seguito [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).

### <a name="savepoints-api"></a>API salvataggio

Problema di GitHub: [#20228](https://github.com/dotnet/efcore/issues/20228).

È stata standardizzata [un'API comune per salvataggio nei provider ADO.NET](https://github.com/dotnet/runtime/issues/33397). Microsoft. Data. sqlite ora supporta questa API, tra cui:

- <xref:System.Data.Common.DbTransaction.Save(System.String)> per creare un salvataggio nella transazione
- <xref:System.Data.Common.DbTransaction.Rollback(System.String)> per eseguire il rollback a una salvataggio precedente
- <xref:System.Data.Common.DbTransaction.Release(System.String)> per rilasciare un salvataggio

L'uso di un salvataggio consente di eseguire il rollback di parte di una transazione senza eseguire il rollback dell'intera transazione. Ad esempio, il codice seguente:

- Crea una transazione
- Invia un aggiornamento al database
- Crea un salvataggio
- Invia un altro aggiornamento al database
- Esegue il rollback al salvataggio creato in precedenza
- Esegui il commit della transazione

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

Questo comporterà il commit del primo aggiornamento nel database, mentre non viene eseguito il commit del secondo aggiornamento dal momento in cui è stato eseguito il rollback di salvataggio prima di eseguire il commit della transazione.

### <a name="command-timeout-in-the-connection-string"></a>Timeout del comando nella stringa di connessione

Problema di GitHub: [#22505](https://github.com/dotnet/efcore/issues/22505). Questa funzionalità è stata aggiunta come contributo da [@nmichels](https://github.com/nmichels) .

I provider ADO.NET supportano due timeout distinti:

- Timeout della connessione, che determina il tempo massimo di attesa durante l'esecuzione di una connessione al database.
- Timeout del comando, che determina il tempo massimo di attesa per il completamento dell'esecuzione di un comando.

Il timeout del comando può essere impostato dal codice usando <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> . Molti provider espongono ora questo timeout del comando nella stringa di connessione. Microsoft. Data. sqlite segue questa tendenza con la `Command Timeout` parola chiave della stringa di connessione. Ad esempio, `"Command Timeout=60;DataSource=test.db"` utilizzerà 60 secondi come timeout predefinito per i comandi creati dalla connessione.

> [!TIP]
> SQLite considera `Default Timeout` come sinonimo di `Command Timeout` e pertanto può essere usato in alternativa se si preferisce.
