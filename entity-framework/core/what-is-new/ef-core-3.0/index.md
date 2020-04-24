---
title: Nuove funzionalità in Entity Framework Core 3.0 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: 39684cdcc17e3baa4b77cf29d54b626294771332
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103139"
---
# <a name="new-features-in-entity-framework-core-30"></a>Nuove funzionalità in Entity Framework Core 3.0

L'elenco seguente include le nuove principali funzionalità di EF Core 3.0.

Essendo una versione principale, EF Core 3.0 contiene anche alcune [modifiche di rilevo](xref:core/what-is-new/ef-core-3.0/breaking-changes), vale a dire miglioramenti delle API che possono avere un impatto negativo sulle applicazioni esistenti.  

## <a name="linq-overhaul"></a>Revisione di LINQ

LINQ consente di scrivere query di database usando il linguaggio .NET preferito, sfruttando i vantaggi di informazioni complete per offrire IntelliSense e il controllo del tipo in fase di compilazione.
LINQ consente anche di scrivere un numero illimitato di query complesse contenenti espressioni arbitrarie (chiamate al metodo oppure operazioni).
La difficoltà principale per i provider LINQ è gestire tutte queste combinazioni.

In EF Core 3.0 il provider LINQ è stato riprogettato per consentire la conversione di più modelli di query in SQL, in modo da generare query efficienti in più casi e impedire che query inefficienti non siano rilevate. Il nuovo provider LINQ è la base che consentirà di offrire nuove funzionalità di query e miglioramenti delle prestazioni nelle versioni future, senza compromettere le applicazioni e i provider di dati esistenti.

### <a name="restricted-client-evaluation"></a>Valutazione client limitata

La modifica più importante in termini di progettazione ha a che fare con la gestione delle espressioni LINQ che non possono essere convertite in parametri o in SQL.

Nelle versioni precedenti EF Core identificava le parti di query che potevano essere convertite in SQL ed eseguiva il resto della query nel client.
Questo tipo di esecuzione sul lato client è utile in alcune situazioni, ma in molti altri casi può generare query inefficienti.

Ad esempio, se in EF Core 2.2 non era possibile convertire un predicato in una chiamata `Where()`, veniva eseguita un'istruzione SQL senza filtro, tutte le righe venivano trasferite dal database e poi filtrate in memoria:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

Questo approccio può essere accettabile se il database contiene un numero ridotto di righe, ma può causare problemi di prestazioni significativi o persino un errore dell'applicazione se il database contiene un numero elevato di righe.

In EF Core 3.0 la valutazione client è stata limitata in modo che avvenga solo nella proiezione di primo livello, essenzialmente con l'ultima chiamata a `Select()`.
Quando EF Core 3.0 rileva espressioni che non possono essere convertite altrove nella query, viene generata un'eccezione in fase di esecuzione.

Per valutare una condizione del predicato nel client come nell'esempio precedente, gli sviluppatori devono ora cambiare in modo esplicito la valutazione della query in LINQ to Objects:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

Vedere la [documentazione sulle modifiche di rilievo](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) per altri dettagli su come vengono influenzate le applicazioni esistenti.

### <a name="single-sql-statement-per-linq-query"></a>Singola istruzione SQL per ogni query LINQ

Esiste un altro aspetto della progettazione che è stato significativamente modificato nella versione 3.0. Ora viene generata sempre una singola istruzione SQL per ogni query LINQ. Nelle versioni precedenti si generavano più istruzioni SQL in casi specifici, chiamate `Include()` convertite per proprietà di esplorazione delle raccolte e query convertite che seguivano determinati modelli con sottoquery. Sebbene in alcuni casi questo approccio fosse comodo e per `Include()` impediva persino di inviare dati ridondanti in rete, l'implementazione era complessa e causava alcuni comportamenti estremamente inefficienti (N + 1 query). In alcune situazioni poteva succedere che i dati restituiti tra più query non fossero coerenti.

Analogamente alla valutazione client, se EF Core 3.0 non può convertire una query LINQ in una singola istruzione SQL, viene generata un'eccezione in fase di esecuzione. Con EF Core è comunque possibile convertire molti dei modelli comuni usati per generare più query in una singola query con JOIN.

## <a name="cosmos-db-support"></a>Supporto di Cosmos DB

Il provider Cosmos DB per EF Core consente agli sviluppatori che hanno familiarità con il modello di programmazione di EF di usare facilmente Azure Cosmos DB come database dell'applicazione. L'obiettivo è quello di rendere alcuni dei vantaggi di Cosmos DB, ad esempio la distribuzione globale, la disponibilità "AlwaysOn", la scalabilità elastica e la bassa latenza, ancora più accessibili per gli sviluppatori .NET. Nel provider è abilitata la maggior parte delle funzionalità di EF Core, ad esempio il rilevamento delle modifiche automatico, LINQ e le conversioni dei valori, con l'API SQL in Cosmos DB.

Per altri dettagli, vedere la [documentazione sul provider di Cosmos DB](xref:core/providers/cosmos/index).

## <a name="c-80-support"></a>Supporto di C# 8.0

EF Core 3.0 sfrutta alcune delle [nuove funzionalità di C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):

### <a name="asynchronous-streams"></a>Flussi asincroni

I risultati delle query asincrone vengono ora esposti usando la nuova interfaccia standard `IAsyncEnumerable<T>` e possono essere usati con `await foreach`.

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

Per altri dettagli, vedere la [documentazione sui flussi asincroni in C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams).

### <a name="nullable-reference-types"></a>Tipi riferimento nullable

Quando questa nuova funzionalità è abilitata nel codice, EF Core esamina il supporto di valori Null delle proprietà del tipo di riferimento e lo applica alle colonne e alle relazioni corrispondenti nel database. Le proprietà di tipi di riferimento non nullable vengono considerate come se avessero l'attributo di annotazione dei dati `[Required]`.

Ad esempio, nella classe seguente le proprietà contrassegnate come di tipo `string?` verranno configurate come facoltative, mentre quelle di tipo `string` verranno configurate come obbligatorie:

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

Per altri dettagli, vedere [Uso dei tipi di riferimento nullable](xref:core/miscellaneous/nullable-reference-types) nella documentazione di EF Core.

## <a name="interception-of-database-operations"></a>Intercettazione di operazioni di database

La nuova API di intercettazione in EF Core 3.0 offre una logica personalizzata da richiamare automaticamente quando si verificano operazioni di database di basso livello come parte del normale funzionamento di EF Core, ad esempio durante l'apertura di connessioni, il commit di transazioni o l'esecuzione di comandi.

Analogamente alle funzionalità di intercettazione in EF 6, gli intercettori consentono di intercettare le operazioni prima o dopo che si verifichino. Quando vengono intercettate prima che si verifichino, è possibile ignorare l'esecuzione e offrire risultati alternativi dalla logica di intercettazione.

Per modificare il testo del comando, è ad esempio possibile creare un `DbCommandInterceptor`:

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

E registrarlo con  `DbContext`:

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>Decompilazione delle viste di database

I tipi di query che rappresentano dati che possono essere letti dal database, ma che non possono essere aggiornati, sono stati rinominati in [tipi di entità senza chiave](xref:core/modeling/keyless-entity-types).
Essendo una soluzione ottimale per il mapping di viste di database nella maggior parte degli scenari, EF Core ora crea automaticamente tipi di entità senza chiave durante la decompilazione delle viste di database.

Ad esempio, usando lo [strumento da riga di comando dotnet ef](xref:core/miscellaneous/cli/dotnet) è possibile digitare quanto riportato di seguito:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

Lo strumento ora creerà automaticamente tipi per viste e tabelle senza chiavi:

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative

A partire da EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o mappato in modo esplicito alla stessa tabella, sarà possibile aggiungere un `Order` senza `OrderDetails` e tutte le proprietà di `OrderDetails`, a eccezione della chiave primaria, verranno mappate a colonne che ammettono i valori Null.

In fase di query, EF Core imposterà `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>EF 6.3 in .NET Core

Non si tratta di una funzionalità di EF Core 3.0, ma è un aspetto importante per molti dei clienti attuali.

Si sa che molte applicazioni esistenti usano versioni precedenti di EF e che la loro conversione in EF Core al solo scopo di sfruttare i vantaggi di .NET Core può richiedere notevoli sforzi.
Per questo motivo, è stato deciso di convertire la prossima versione di EF 6 in modo che possa essere eseguita in .NET Core 3.0.

Per altri dettagli, vedere [Novità di EF6](xref:ef6/what-is-new/index).

## <a name="postponed-features"></a>Funzionalità rimandate

Alcune funzionalità pianificate originariamente per EF Core 3.0 sono state rimandate alle versioni future:

- Possibilità di ignorare parti di un modello nelle migrazioni, registrata come [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).
- Entità contenitore delle proprietà, registrata come due problemi distinti: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) per le entità di tipo condiviso e [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) per il supporto del mapping delle proprietà indicizzate.
