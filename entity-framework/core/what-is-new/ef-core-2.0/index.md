---
title: Novità di EF Core 2.0 - EF Core
description: Modifiche e miglioramenti di Entity Framework Core 2.0
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: e6683e2b70bce2b07881f3bfd047d54768b988f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620660"
---
# <a name="new-features-in-ef-core-20"></a>Nuove funzionalità di EF Core 2.0

## <a name="net-standard-20"></a>.NET Standard 2.0

EF Core ha ora come obiettivo .NET Standard 2.0 e funziona con .NET Core 2.0, .NET Framework 4.6.1 e altre librerie che implementano .NET Standard 2.0.
Vedere [Implementazioni di .NET supportate](xref:core/platforms/index) per altri dettagli sulle implementazioni supportate.

## <a name="modeling"></a>Modellazione

### <a name="table-splitting"></a>Suddivisione di tabelle

È ora possibile eseguire il mapping di due o più tipi di entità alla stessa tabella in cui le colonne di chiave primaria verranno condivise e ogni riga corrisponderà a due o più entità.

Per usare la suddivisione di tabelle è necessario configurare una relazione di identificazione (in cui le proprietà di chiave esterna formano la chiave primaria) tra tutti i tipi di entità che condividono la tabella:

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

Leggere la [sezione sulla suddivisione di tabelle](xref:core/modeling/table-splitting) per altre informazioni su questa funzionalità.

### <a name="owned-types"></a>Tipi di proprietà

Un tipo di entità di proprietà può condividere lo stesso tipo .NET con un altro tipo di entità di proprietà, ma poiché non può essere identificato soltanto dal tipo .NET è necessario che sia presente una navigazione verso di esso da un altro tipo di entità. L'entità contenente la navigazione che lo definisce è il proprietario. Quando si esegue una query sul proprietario, i tipi di proprietà saranno inclusi per impostazione predefinita.

Per convenzione viene creata una chiave primaria shadow per il tipo di proprietà e ne viene eseguito il mapping alla stessa tabella del proprietario usando la suddivisione di tabelle. Ciò consente di usare i tipi di proprietà in modo simile ai tipi complessi in EF6:

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

Leggere la [sezione sui tipi di entità di proprietà](xref:core/modeling/owned-entities) per altre informazioni su questa funzionalità.

### <a name="model-level-query-filters"></a>Filtri di query a livello di modello

EF Core 2.0 include una nuova funzionalità denominata filtri di query a livello di modello. Questa funzionalità consente di definire i predicati di query LINQ (un'espressione booleana in genere passata all'operatore di query Where di LINQ) direttamente nei tipi di entità nel modello di metadati (solitamente in OnModelCreating). Questi filtri vengono automaticamente applicati a qualsiasi query LINQ che interessa questi tipi di entità, inclusi quelli a cui si fa riferimento in modo indiretto, ad esempio tramite l'uso di riferimenti alle proprietà Include o di navigazione diretta. Alcune applicazioni comuni di questa funzionalità sono:

- Eliminazione temporanea, un tipo di entità definisce una proprietà IsDeleted.
- Multi-tenancy, un tipo di entità definisce una proprietà TenantId.

Di seguito è riportato un semplice esempio che illustra la funzionalità per questi due scenari:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

Viene definito un filtro a livello di modello che implementa il multi-tenancy e l'eliminazione temporanea per le istanze del tipo di entità `Post`. Si noti l'uso di una `DbContext`proprietà a livello di istanza: `TenantId`. I filtri a livello di modello usano il valore dell'istanza del contesto corretta, ovvero l'istanza del contesto che esegue la query.

È possibile disabilitare i filtri per le singole query LINQ usando l'operatore IgnoreQueryFilters().

#### <a name="limitations"></a>Limitazioni

- I riferimenti di navigazione non sono consentiti. Questa funzionalità potrà essere aggiunta sulla base del feedback ricevuto.
- I filtri possono essere definiti solo nel tipo di entità radice di una gerarchia.

### <a name="database-scalar-function-mapping"></a>Mapping di funzione scalare del database

EF Core 2.0 include un importante contributo di [Paul Middleton](https://github.com/pmiddleton) che consente il mapping delle funzioni scalari del database agli stub di metodo in modo da poterli usare nelle query LINQ e convertire in SQL.

Di seguito è riportata una breve descrizione di come usare la funzionalità:

Dichiarare un metodo statico in `DbContext` e annotarlo con `DbFunctionAttribute`:

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

I metodi come questo vengono registrati automaticamente. Dopo la registrazione, le chiamate al metodo in una query LINQ possono essere convertite in chiamate a funzioni in SQL:

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

Alcuni punti di cui tenere conto:

- Per convenzione, durante la generazione di SQL, il nome del metodo viene usato come nome di una funzione (in questo caso una funzione definita dall'utente), ma è possibile eseguire l'override del nome e dello schema durante la registrazione del metodo.
- Attualmente sono supportate solo le funzioni scalari.
- È necessario creare la funzione mappata nel database. La funzione non viene creata dalle migrazioni EF Core.

### <a name="self-contained-type-configuration-for-code-first"></a>Configurazione dei tipi completa per Code First

In EF6 è possibile incapsulare la configurazione Code First di un tipo di entità specifico derivandola da *EntityTypeConfiguration*. In EF Core 2.0 questo modello viene di nuovo reso disponibile:

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>Prestazioni elevate

### <a name="dbcontext-pooling"></a>Pooling DbContext

Il modello di base per l'uso di EF Core in un'applicazione ASP.NET Core in genere comporta la registrazione di un tipo DbContext personalizzato nel sistema di inserimento delle dipendenze e il successivo recupero delle istanze del tipo tramite i parametri del costruttore nei controller. Ciò significa che per ogni richiesta viene creata una nuova istanza di DbContext.

La versione 2.0 presenta un nuovo modo di registrare i tipi DbContext personalizzati nell'inserimento delle dipendenze che introduce in modo trasparente un pool di istanze DbContext riutilizzabili. Per usare il pooling DbContext, usare `AddDbContextPool` anziché `AddDbContext` durante la registrazione del servizio:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Se viene usato questo metodo, al momento della richiesta di un'istanza di DbContext da parte di un controller, verrà prima verificata la presenza di un'istanza disponibile nel pool. Dopo che l'elaborazione della richiesta è stata finalizzata, lo stato dell'istanza viene reimpostato e quest'ultima viene restituita al pool.

Questo metodo è concettualmente simile al funzionamento del pool di connessioni nei provider ADO.NET e offre il vantaggio di ridurre alcuni dei costi di inizializzazione dell'istanza di DbContext.

### <a name="limitations"></a>Limitazioni

Il nuovo metodo introduce alcune limitazioni nelle operazioni possibili nel metodo `OnConfiguring()` di DbContext.

> [!WARNING]  
> Evitare di usare il pooling DbContext se nella classe DbContext derivata si mantiene uno stato (ad esempio, campi privati) che non deve essere condiviso tra le richieste. Prima di aggiungere un'istanza di DbContext al pool, EF Core reimposterà solo lo stato di cui è a conoscenza.

### <a name="explicitly-compiled-queries"></a>Query compilate esplicite

Questa è la seconda funzionalità di ottimizzazione delle prestazioni progettata per offrire vantaggi in scenari di vasta scala.

Le API per le query manuali o compilate in modo esplicito sono disponibili nelle versioni precedenti di EF e in LINQ to SQL per consentire alle applicazioni di memorizzare nella cache la conversione delle query in modo da poterle calcolare una sola volta ed eseguire molte volte.

Sebbene in genere EF Core sia in grado di compilare le query e memorizzarle automaticamente nella cache in base a una rappresentazione con hash delle espressioni di query, questo meccanismo può essere utile per ottenere un lieve miglioramento delle prestazioni in quanto evita il calcolo dell'hash e la ricerca nella cache consentendo all'applicazione di usare una query già compilata tramite la chiamata di un delegato.

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>Rilevamento modifiche

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>Il metodo Attach consente di tracciare un grafo delle entità nuove ed esistenti

EF Core supporta la generazione automatica di valori di chiave tramite una varietà di meccanismi. Quando si usa questa funzionalità viene generato un valore se la proprietà chiave è l'impostazione predefinita CLR, in genere zero o Null. Ciò significa che è possibile passare un grafo delle entità a `DbContext.Attach` o `DbSet.Attach` e EF Core contrassegnerà le entità con una chiave già impostata come `Unchanged`, mentre le entità senza una chiave impostata verranno contrassegnate come `Added`. Grazie a questa funzionalità, sarà facile allegare un grafo di entità nuove ed esistenti miste quando si usano le chiavi generate. `DbContext.Update` e `DbSet.Update` funzionano allo stesso modo, salvo che le entità con una chiave impostata vengono contrassegnate come `Modified` anziché `Unchanged`.

## <a name="query"></a>Query

### <a name="improved-linq-translation"></a>Traduzione LINQ migliorata

Consente di eseguire correttamente un maggior numero di query, con una quantità superiore di logica valutata nel database (anziché in memoria) e una quantità inferiore di dati inutilmente recuperati dal database.

### <a name="groupjoin-improvements"></a>Miglioramenti di GroupJoin

Il codice SQL generato per i join di gruppo è stato migliorato. I join di gruppo sono nella maggior parte dei casi un risultato delle sottoquery sulle proprietà di navigazione facoltative.

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>Interpolazione di stringhe in FromSql e ExecuteSqlCommand

C# 6 ha introdotto l'interpolazione di stringhe, una funzionalità che consente di incorporare direttamente le espressioni C# nei valori letterali di stringa offrendo un ottimo modo per compilare le stringhe in fase di esecuzione. In EF Core 2.0 uno speciale supporto per le stringhe interpolate è stato aggiunto alle due API principali che accettano stringhe SQL non elaborate: `FromSql` e `ExecuteSqlCommand`. Questo nuovo supporto consente di usare l'interpolazione di stringhe C# in modo "sicuro", ovvero in modo da garantire la protezione dagli errori SQL injection comuni che possono verificarsi durante la costruzione dinamica di SQL in fase di esecuzione.

Ecco un esempio:

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

In questo esempio sono presenti due variabili incorporate nella stringa di formato SQL. EF Core genererà il codice SQL seguente:

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>EF.Functions.Like()

È stata aggiunta la proprietà EF.Functions che può essere usata da EF Core o dai provider per definire metodi che eseguono il mapping alle funzioni o agli operatori del database in modo che sia possibile chiamarli nelle query LINQ. Il primo esempio di questo metodo è Like():

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

Notare che Like() include un'implementazione in memoria che può rivelarsi utile quando si usa un database in memoria o quando la valutazione del predicato deve essere eseguita sul lato client.

## <a name="database-management"></a>Gestione di database

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>Hook di pluralizzazione per lo scaffolding DbContext

EF Core 2.0 introduce un nuovo servizio *IPluralizer* usato per rendere singolari i nomi dei tipi di entità e rendere plurali i nomi DbSet. L'implementazione predefinita è no-op, quindi si tratta semplicemente di un hook a cui gli sviluppatori possono facilmente collegare i propri pluralizer.

Per uno sviluppatore che desideri collegarlo al proprio pluralizer, il servizio ha l'aspetto seguente:

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>Altro

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>Spostamento del provider SQLite ADO.NET in SQLitePCL.raw

Rende la soluzione più solida in Microsoft.Data.Sqlite per la distribuzione di file binari SQLite nativi in diverse piattaforme.

### <a name="only-one-provider-per-model"></a>Un solo provider per modello

Aumenta in modo significativo le modalità di interazione dei provider con il modello e semplifica il funzionamento di convenzioni, annotazioni e API Fluent con i diversi provider.

EF Core 2.0 ora compila un elemento [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) diverso per ogni provider in uso. L'operazione è in genere trasparente per l'applicazione. Questa scelta ha agevolato la semplificazione delle API di metadati di livello inferiore in modo tale che l'accesso ai *concetti di metadati relazionali comuni* viene sempre eseguito tramite una chiamata a `.Relational` anziché `.SqlServer`, `.Sqlite` e così via.

### <a name="consolidated-logging-and-diagnostics"></a>Registrazione e diagnostica consolidate

I meccanismi di registrazione (basati su ILogger) e diagnostica (basati su DiagnosticSource) ora condividono una maggiore quantità di codice.

Nella versione 2.0 gli ID evento per i messaggi inviati a un ILogger sono stati modificati. Gli ID evento sono ora univoci nel codice EF Core. Questi messaggi ora seguono inoltre il modello standard per la registrazione strutturata usato, ad esempio, da MVC.

Anche le categorie del logger sono state modificate. È ora disponibile un set di categorie ben noto accessibile tramite [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).

Gli eventi DiagnosticSource ora usano gli stessi nomi di ID evento dei messaggi `ILogger` corrispondenti.
