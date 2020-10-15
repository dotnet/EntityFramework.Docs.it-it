---
title: Novità di EF Core 2.0 - EF Core
description: Modifiche e miglioramenti di Entity Framework Core 2.0
author: ajcvickers
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 7438d8ad1a5ade971af71186a20ec57fd83713de
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063452"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="580d0-103">Nuove funzionalità di EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="580d0-103">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="580d0-104">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="580d0-104">.NET Standard 2.0</span></span>

<span data-ttu-id="580d0-105">EF Core ha ora come obiettivo .NET Standard 2.0 e funziona con .NET Core 2.0, .NET Framework 4.6.1 e altre librerie che implementano .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="580d0-105">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="580d0-106">Vedere [Implementazioni di .NET supportate](xref:core/platforms/index) per altri dettagli sulle implementazioni supportate.</span><span class="sxs-lookup"><span data-stu-id="580d0-106">See [Supported .NET Implementations](xref:core/platforms/index) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="580d0-107">Modellazione</span><span class="sxs-lookup"><span data-stu-id="580d0-107">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="580d0-108">Suddivisione di tabelle</span><span class="sxs-lookup"><span data-stu-id="580d0-108">Table splitting</span></span>

<span data-ttu-id="580d0-109">È ora possibile eseguire il mapping di due o più tipi di entità alla stessa tabella in cui le colonne di chiave primaria verranno condivise e ogni riga corrisponderà a due o più entità.</span><span class="sxs-lookup"><span data-stu-id="580d0-109">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="580d0-110">Per usare la suddivisione di tabelle è necessario configurare una relazione di identificazione (in cui le proprietà di chiave esterna formano la chiave primaria) tra tutti i tipi di entità che condividono la tabella:</span><span class="sxs-lookup"><span data-stu-id="580d0-110">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

```csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

<span data-ttu-id="580d0-111">Leggere la [sezione sulla suddivisione di tabelle](xref:core/modeling/table-splitting) per altre informazioni su questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="580d0-111">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="580d0-112">Tipi di proprietà</span><span class="sxs-lookup"><span data-stu-id="580d0-112">Owned types</span></span>

<span data-ttu-id="580d0-113">Un tipo di entità di proprietà può condividere lo stesso tipo .NET con un altro tipo di entità di proprietà, ma poiché non può essere identificato soltanto dal tipo .NET è necessario che sia presente una navigazione verso di esso da un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="580d0-113">An owned entity type can share the same .NET type with another owned entity type, but since it cannot be identified just by the .NET type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="580d0-114">L'entità contenente la navigazione che lo definisce è il proprietario.</span><span class="sxs-lookup"><span data-stu-id="580d0-114">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="580d0-115">Quando si esegue una query sul proprietario, i tipi di proprietà saranno inclusi per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="580d0-115">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="580d0-116">Per convenzione viene creata una chiave primaria shadow per il tipo di proprietà e ne viene eseguito il mapping alla stessa tabella del proprietario usando la suddivisione di tabelle.</span><span class="sxs-lookup"><span data-stu-id="580d0-116">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="580d0-117">Ciò consente di usare i tipi di proprietà in modo simile ai tipi complessi in EF6:</span><span class="sxs-lookup"><span data-stu-id="580d0-117">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

```csharp
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

<span data-ttu-id="580d0-118">Leggere la [sezione sui tipi di entità di proprietà](xref:core/modeling/owned-entities) per altre informazioni su questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="580d0-118">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="580d0-119">Filtri di query a livello di modello</span><span class="sxs-lookup"><span data-stu-id="580d0-119">Model-level query filters</span></span>

<span data-ttu-id="580d0-120">EF Core 2.0 include una nuova funzionalità denominata filtri di query a livello di modello.</span><span class="sxs-lookup"><span data-stu-id="580d0-120">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="580d0-121">Questa funzionalità consente di definire i predicati di query LINQ (un'espressione booleana in genere passata all'operatore di query Where di LINQ) direttamente nei tipi di entità nel modello di metadati (solitamente in OnModelCreating).</span><span class="sxs-lookup"><span data-stu-id="580d0-121">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="580d0-122">Questi filtri vengono automaticamente applicati a qualsiasi query LINQ che interessa questi tipi di entità, inclusi quelli a cui si fa riferimento in modo indiretto, ad esempio tramite l'uso di riferimenti alle proprietà Include o di navigazione diretta.</span><span class="sxs-lookup"><span data-stu-id="580d0-122">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="580d0-123">Alcune applicazioni comuni di questa funzionalità sono:</span><span class="sxs-lookup"><span data-stu-id="580d0-123">Some common applications of this feature are:</span></span>

- <span data-ttu-id="580d0-124">Eliminazione temporanea, un tipo di entità definisce una proprietà IsDeleted.</span><span class="sxs-lookup"><span data-stu-id="580d0-124">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="580d0-125">Multi-tenancy, un tipo di entità definisce una proprietà TenantId.</span><span class="sxs-lookup"><span data-stu-id="580d0-125">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="580d0-126">Di seguito è riportato un semplice esempio che illustra la funzionalità per questi due scenari:</span><span class="sxs-lookup"><span data-stu-id="580d0-126">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

```csharp
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

<span data-ttu-id="580d0-127">Viene definito un filtro a livello di modello che implementa il multi-tenancy e l'eliminazione temporanea per le istanze del tipo di entità `Post`.</span><span class="sxs-lookup"><span data-stu-id="580d0-127">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="580d0-128">Si noti l'uso di una `DbContext`proprietà a livello di istanza: `TenantId`.</span><span class="sxs-lookup"><span data-stu-id="580d0-128">Note the use of a `DbContext` instance-level property: `TenantId`.</span></span> <span data-ttu-id="580d0-129">I filtri a livello di modello usano il valore dell'istanza del contesto corretta, ovvero l'istanza del contesto che esegue la query.</span><span class="sxs-lookup"><span data-stu-id="580d0-129">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="580d0-130">È possibile disabilitare i filtri per le singole query LINQ usando l'operatore IgnoreQueryFilters().</span><span class="sxs-lookup"><span data-stu-id="580d0-130">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="580d0-131">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="580d0-131">Limitations</span></span>

- <span data-ttu-id="580d0-132">I riferimenti di navigazione non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="580d0-132">Navigation references are not allowed.</span></span> <span data-ttu-id="580d0-133">Questa funzionalità potrà essere aggiunta sulla base del feedback ricevuto.</span><span class="sxs-lookup"><span data-stu-id="580d0-133">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="580d0-134">I filtri possono essere definiti solo nel tipo di entità radice di una gerarchia.</span><span class="sxs-lookup"><span data-stu-id="580d0-134">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="580d0-135">Mapping di funzione scalare del database</span><span class="sxs-lookup"><span data-stu-id="580d0-135">Database scalar function mapping</span></span>

<span data-ttu-id="580d0-136">EF Core 2.0 include un importante contributo di [Paul Middleton](https://github.com/pmiddleton) che consente il mapping delle funzioni scalari del database agli stub di metodo in modo da poterli usare nelle query LINQ e convertire in SQL.</span><span class="sxs-lookup"><span data-stu-id="580d0-136">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="580d0-137">Di seguito è riportata una breve descrizione di come usare la funzionalità:</span><span class="sxs-lookup"><span data-stu-id="580d0-137">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="580d0-138">Dichiarare un metodo statico in `DbContext` e annotarlo con `DbFunctionAttribute`:</span><span class="sxs-lookup"><span data-stu-id="580d0-138">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

<span data-ttu-id="580d0-139">I metodi come questo vengono registrati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="580d0-139">Methods like this are automatically registered.</span></span> <span data-ttu-id="580d0-140">Dopo la registrazione, le chiamate al metodo in una query LINQ possono essere convertite in chiamate a funzioni in SQL:</span><span class="sxs-lookup"><span data-stu-id="580d0-140">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

```csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="580d0-141">Alcuni punti di cui tenere conto:</span><span class="sxs-lookup"><span data-stu-id="580d0-141">A few things to note:</span></span>

- <span data-ttu-id="580d0-142">Per convenzione, durante la generazione di SQL, il nome del metodo viene usato come nome di una funzione (in questo caso una funzione definita dall'utente), ma è possibile eseguire l'override del nome e dello schema durante la registrazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="580d0-142">By convention the name of the method is used as the name of a function (in this case a user-defined function) when generating the SQL, but you can override the name and schema during method registration.</span></span>
- <span data-ttu-id="580d0-143">Attualmente sono supportate solo le funzioni scalari.</span><span class="sxs-lookup"><span data-stu-id="580d0-143">Currently only scalar functions are supported.</span></span>
- <span data-ttu-id="580d0-144">È necessario creare la funzione mappata nel database.</span><span class="sxs-lookup"><span data-stu-id="580d0-144">You must create the mapped function in the database.</span></span> <span data-ttu-id="580d0-145">La funzione non viene creata dalle migrazioni EF Core.</span><span class="sxs-lookup"><span data-stu-id="580d0-145">EF Core migrations will not take care of creating it.</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="580d0-146">Configurazione dei tipi completa per Code First</span><span class="sxs-lookup"><span data-stu-id="580d0-146">Self-contained type configuration for code first</span></span>

<span data-ttu-id="580d0-147">In EF6 è possibile incapsulare la configurazione Code First di un tipo di entità specifico derivandola da *EntityTypeConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="580d0-147">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="580d0-148">In EF Core 2.0 questo modello viene di nuovo reso disponibile:</span><span class="sxs-lookup"><span data-stu-id="580d0-148">In EF Core 2.0 we are bringing this pattern back:</span></span>

```csharp
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

## <a name="high-performance"></a><span data-ttu-id="580d0-149">Prestazioni elevate</span><span class="sxs-lookup"><span data-stu-id="580d0-149">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="580d0-150">Pooling DbContext</span><span class="sxs-lookup"><span data-stu-id="580d0-150">DbContext pooling</span></span>

<span data-ttu-id="580d0-151">Il modello di base per l'uso di EF Core in un'applicazione ASP.NET Core in genere comporta la registrazione di un tipo DbContext personalizzato nel sistema di inserimento delle dipendenze e il successivo recupero delle istanze del tipo tramite i parametri del costruttore nei controller.</span><span class="sxs-lookup"><span data-stu-id="580d0-151">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="580d0-152">Ciò significa che per ogni richiesta viene creata una nuova istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="580d0-152">This means a new instance of the DbContext is created for each request.</span></span>

<span data-ttu-id="580d0-153">La versione 2.0 presenta un nuovo modo di registrare i tipi DbContext personalizzati nell'inserimento delle dipendenze che introduce in modo trasparente un pool di istanze DbContext riutilizzabili.</span><span class="sxs-lookup"><span data-stu-id="580d0-153">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="580d0-154">Per usare il pooling DbContext, usare `AddDbContextPool` anziché `AddDbContext` durante la registrazione del servizio:</span><span class="sxs-lookup"><span data-stu-id="580d0-154">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="580d0-155">Se viene usato questo metodo, al momento della richiesta di un'istanza di DbContext da parte di un controller, verrà prima verificata la presenza di un'istanza disponibile nel pool.</span><span class="sxs-lookup"><span data-stu-id="580d0-155">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="580d0-156">Dopo che l'elaborazione della richiesta è stata finalizzata, lo stato dell'istanza viene reimpostato e quest'ultima viene restituita al pool.</span><span class="sxs-lookup"><span data-stu-id="580d0-156">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="580d0-157">Questo metodo è concettualmente simile al funzionamento del pool di connessioni nei provider ADO.NET e offre il vantaggio di ridurre alcuni dei costi di inizializzazione dell'istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="580d0-157">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="580d0-158">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="580d0-158">Limitations</span></span>

<span data-ttu-id="580d0-159">Il nuovo metodo introduce alcune limitazioni nelle operazioni possibili nel metodo `OnConfiguring()` di DbContext.</span><span class="sxs-lookup"><span data-stu-id="580d0-159">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]
> <span data-ttu-id="580d0-160">Evitare di usare il pooling DbContext se nella classe DbContext derivata si mantiene uno stato (ad esempio, campi privati) che non deve essere condiviso tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="580d0-160">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="580d0-161">Prima di aggiungere un'istanza di DbContext al pool, EF Core reimposterà solo lo stato di cui è a conoscenza.</span><span class="sxs-lookup"><span data-stu-id="580d0-161">EF Core will only reset the state that it is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="580d0-162">Query compilate esplicite</span><span class="sxs-lookup"><span data-stu-id="580d0-162">Explicitly compiled queries</span></span>

<span data-ttu-id="580d0-163">Questa è la seconda funzionalità di ottimizzazione delle prestazioni progettata per offrire vantaggi in scenari di vasta scala.</span><span class="sxs-lookup"><span data-stu-id="580d0-163">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="580d0-164">Le API per le query manuali o compilate in modo esplicito sono disponibili nelle versioni precedenti di EF e in LINQ to SQL per consentire alle applicazioni di memorizzare nella cache la conversione delle query in modo da poterle calcolare una sola volta ed eseguire molte volte.</span><span class="sxs-lookup"><span data-stu-id="580d0-164">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="580d0-165">Sebbene in genere EF Core sia in grado di compilare le query e memorizzarle automaticamente nella cache in base a una rappresentazione con hash delle espressioni di query, questo meccanismo può essere utile per ottenere un lieve miglioramento delle prestazioni in quanto evita il calcolo dell'hash e la ricerca nella cache consentendo all'applicazione di usare una query già compilata tramite la chiamata di un delegato.</span><span class="sxs-lookup"><span data-stu-id="580d0-165">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

```csharp
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

## <a name="change-tracking"></a><span data-ttu-id="580d0-166">Rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="580d0-166">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="580d0-167">Il metodo Attach consente di tracciare un grafo delle entità nuove ed esistenti</span><span class="sxs-lookup"><span data-stu-id="580d0-167">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="580d0-168">EF Core supporta la generazione automatica di valori di chiave tramite una varietà di meccanismi.</span><span class="sxs-lookup"><span data-stu-id="580d0-168">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="580d0-169">Quando si usa questa funzionalità viene generato un valore se la proprietà chiave è l'impostazione predefinita CLR, in genere zero o Null.</span><span class="sxs-lookup"><span data-stu-id="580d0-169">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="580d0-170">Ciò significa che è possibile passare un grafo delle entità a `DbContext.Attach` o `DbSet.Attach` e EF Core contrassegnerà le entità con una chiave già impostata come `Unchanged`, mentre le entità senza una chiave impostata verranno contrassegnate come `Added`.</span><span class="sxs-lookup"><span data-stu-id="580d0-170">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="580d0-171">Grazie a questa funzionalità, sarà facile allegare un grafo di entità nuove ed esistenti miste quando si usano le chiavi generate.</span><span class="sxs-lookup"><span data-stu-id="580d0-171">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="580d0-172">`DbContext.Update` e `DbSet.Update` funzionano allo stesso modo, salvo che le entità con una chiave impostata vengono contrassegnate come `Modified` anziché `Unchanged`.</span><span class="sxs-lookup"><span data-stu-id="580d0-172">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="580d0-173">Query</span><span class="sxs-lookup"><span data-stu-id="580d0-173">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="580d0-174">Traduzione LINQ migliorata</span><span class="sxs-lookup"><span data-stu-id="580d0-174">Improved LINQ translation</span></span>

<span data-ttu-id="580d0-175">Consente di eseguire correttamente un maggior numero di query, con una quantità superiore di logica valutata nel database (anziché in memoria) e una quantità inferiore di dati inutilmente recuperati dal database.</span><span class="sxs-lookup"><span data-stu-id="580d0-175">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="580d0-176">Miglioramenti di GroupJoin</span><span class="sxs-lookup"><span data-stu-id="580d0-176">GroupJoin improvements</span></span>

<span data-ttu-id="580d0-177">Il codice SQL generato per i join di gruppo è stato migliorato.</span><span class="sxs-lookup"><span data-stu-id="580d0-177">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="580d0-178">I join di gruppo sono nella maggior parte dei casi un risultato delle sottoquery sulle proprietà di navigazione facoltative.</span><span class="sxs-lookup"><span data-stu-id="580d0-178">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="580d0-179">Interpolazione di stringhe in FromSql e ExecuteSqlCommand</span><span class="sxs-lookup"><span data-stu-id="580d0-179">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="580d0-180">C# 6 ha introdotto l'interpolazione di stringhe, una funzionalità che consente di incorporare direttamente le espressioni C# nei valori letterali di stringa offrendo un ottimo modo per compilare le stringhe in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="580d0-180">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="580d0-181">In EF Core 2.0 uno speciale supporto per le stringhe interpolate è stato aggiunto alle due API principali che accettano stringhe SQL non elaborate: `FromSql` e `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="580d0-181">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="580d0-182">Questo nuovo supporto consente di usare l'interpolazione di stringhe C# in modo "sicuro",</span><span class="sxs-lookup"><span data-stu-id="580d0-182">This new support allows C# string interpolation to be used in a "safe" manner.</span></span> <span data-ttu-id="580d0-183">ovvero in modo da garantire la protezione dagli errori SQL injection comuni che possono verificarsi durante la costruzione dinamica di SQL in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="580d0-183">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="580d0-184">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="580d0-184">Here is an example:</span></span>

```csharp
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

<span data-ttu-id="580d0-185">In questo esempio sono presenti due variabili incorporate nella stringa di formato SQL.</span><span class="sxs-lookup"><span data-stu-id="580d0-185">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="580d0-186">EF Core genererà il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="580d0-186">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="580d0-187">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="580d0-187">EF.Functions.Like()</span></span>

<span data-ttu-id="580d0-188">È stata aggiunta la proprietà EF.Functions che può essere usata da EF Core o dai provider per definire metodi che eseguono il mapping alle funzioni o agli operatori del database in modo che sia possibile chiamarli nelle query LINQ.</span><span class="sxs-lookup"><span data-stu-id="580d0-188">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="580d0-189">Il primo esempio di questo metodo è Like():</span><span class="sxs-lookup"><span data-stu-id="580d0-189">The first example of such a method is Like():</span></span>

```csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="580d0-190">Notare che Like() include un'implementazione in memoria che può rivelarsi utile quando si usa un database in memoria o quando la valutazione del predicato deve essere eseguita sul lato client.</span><span class="sxs-lookup"><span data-stu-id="580d0-190">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="580d0-191">Gestione di database</span><span class="sxs-lookup"><span data-stu-id="580d0-191">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="580d0-192">Hook di pluralizzazione per lo scaffolding DbContext</span><span class="sxs-lookup"><span data-stu-id="580d0-192">Pluralization hook for DbContext scaffolding</span></span>

<span data-ttu-id="580d0-193">EF Core 2.0 introduce un nuovo servizio *IPluralizer* usato per rendere singolari i nomi dei tipi di entità e rendere plurali i nomi DbSet.</span><span class="sxs-lookup"><span data-stu-id="580d0-193">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="580d0-194">L'implementazione predefinita è no-op, quindi si tratta semplicemente di un hook a cui gli sviluppatori possono facilmente collegare i propri pluralizer.</span><span class="sxs-lookup"><span data-stu-id="580d0-194">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="580d0-195">Per uno sviluppatore che desideri collegarlo al proprio pluralizer, il servizio ha l'aspetto seguente:</span><span class="sxs-lookup"><span data-stu-id="580d0-195">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

```csharp
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

## <a name="others"></a><span data-ttu-id="580d0-196">Altro</span><span class="sxs-lookup"><span data-stu-id="580d0-196">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="580d0-197">Spostamento del provider SQLite ADO.NET in SQLitePCL.raw</span><span class="sxs-lookup"><span data-stu-id="580d0-197">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>

<span data-ttu-id="580d0-198">Rende la soluzione più solida in Microsoft.Data.Sqlite per la distribuzione di file binari SQLite nativi in diverse piattaforme.</span><span class="sxs-lookup"><span data-stu-id="580d0-198">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="580d0-199">Un solo provider per modello</span><span class="sxs-lookup"><span data-stu-id="580d0-199">Only one provider per model</span></span>

<span data-ttu-id="580d0-200">Aumenta in modo significativo le modalità di interazione dei provider con il modello e semplifica il funzionamento di convenzioni, annotazioni e API Fluent con i diversi provider.</span><span class="sxs-lookup"><span data-stu-id="580d0-200">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="580d0-201">EF Core 2.0 ora compila un elemento [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) diverso per ogni provider in uso.</span><span class="sxs-lookup"><span data-stu-id="580d0-201">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="580d0-202">L'operazione è in genere trasparente per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="580d0-202">This is usually transparent to the application.</span></span> <span data-ttu-id="580d0-203">Questa scelta ha agevolato la semplificazione delle API di metadati di livello inferiore in modo tale che l'accesso ai *concetti di metadati relazionali comuni* viene sempre eseguito tramite una chiamata a `.Relational` anziché `.SqlServer`, `.Sqlite` e così via.</span><span class="sxs-lookup"><span data-stu-id="580d0-203">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="580d0-204">Registrazione e diagnostica consolidate</span><span class="sxs-lookup"><span data-stu-id="580d0-204">Consolidated logging and diagnostics</span></span>

<span data-ttu-id="580d0-205">I meccanismi di registrazione (basati su ILogger) e diagnostica (basati su DiagnosticSource) ora condividono una maggiore quantità di codice.</span><span class="sxs-lookup"><span data-stu-id="580d0-205">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="580d0-206">Nella versione 2.0 gli ID evento per i messaggi inviati a un ILogger sono stati modificati.</span><span class="sxs-lookup"><span data-stu-id="580d0-206">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="580d0-207">Gli ID evento sono ora univoci nel codice EF Core.</span><span class="sxs-lookup"><span data-stu-id="580d0-207">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="580d0-208">Questi messaggi ora seguono inoltre il modello standard per la registrazione strutturata usato, ad esempio, da MVC.</span><span class="sxs-lookup"><span data-stu-id="580d0-208">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="580d0-209">Anche le categorie del logger sono state modificate.</span><span class="sxs-lookup"><span data-stu-id="580d0-209">Logger categories have also changed.</span></span> <span data-ttu-id="580d0-210">È ora disponibile un set di categorie ben noto accessibile tramite [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span><span class="sxs-lookup"><span data-stu-id="580d0-210">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="580d0-211">Gli eventi DiagnosticSource ora usano gli stessi nomi di ID evento dei messaggi `ILogger` corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="580d0-211">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
