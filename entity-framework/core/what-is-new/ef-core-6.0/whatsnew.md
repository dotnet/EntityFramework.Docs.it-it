---
title: Novità di EF Core 6,0
description: Panoramica delle nuove funzionalità di EF Core 6,0
author: ajcvickers
ms.date: 03/08/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: 15ab49f60d8831c60e599d8c06b3700aace74bda
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023523"
---
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="2a206-103">Novità di EF Core 6,0</span><span class="sxs-lookup"><span data-stu-id="2a206-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="2a206-104">EF Core 6,0 è attualmente in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2a206-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="2a206-105">Questo articolo contiene una panoramica delle modifiche interessanti introdotte in ogni anteprima.</span><span class="sxs-lookup"><span data-stu-id="2a206-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="2a206-106">Questa pagina non duplica il [piano per EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan).</span><span class="sxs-lookup"><span data-stu-id="2a206-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="2a206-107">Il piano descrive i temi generali per EF Core 6,0, inclusi tutti gli elementi che si prevede di includere prima di distribuire la versione finale.</span><span class="sxs-lookup"><span data-stu-id="2a206-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

> [!TIP]
> <span data-ttu-id="2a206-108">È possibile eseguire ed eseguire il debug in tutti gli esempi di anteprima 1 mostrati di seguito [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="2a206-108">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

## <a name="ef-core-60-preview-2"></a><span data-ttu-id="2a206-109">EF Core 6,0 Preview 2</span><span class="sxs-lookup"><span data-stu-id="2a206-109">EF Core 6.0 Preview 2</span></span>

### <a name="preserve-synchronization-context-in-savechangesasync"></a><span data-ttu-id="2a206-110">Mantieni il contesto di sincronizzazione in SaveChangesAsync</span><span class="sxs-lookup"><span data-stu-id="2a206-110">Preserve synchronization context in SaveChangesAsync</span></span>

<span data-ttu-id="2a206-111">Problema di GitHub: [#23971](https://github.com/dotnet/efcore/issues/23971).</span><span class="sxs-lookup"><span data-stu-id="2a206-111">GitHub Issue: [#23971](https://github.com/dotnet/efcore/issues/23971).</span></span>

<span data-ttu-id="2a206-112">[Il codice EF Core nella versione 5,0](https://github.com/dotnet/efcore/issues/10164) è stato modificato in modo da impostare <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> su `false` in tutte le posizioni in cui è presente il `await` codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="2a206-112">We [changed the EF Core code in the 5.0 release](https://github.com/dotnet/efcore/issues/10164) to set <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> to `false` in all places where we `await` async code.</span></span> <span data-ttu-id="2a206-113">Si tratta in genere di una scelta migliore per l'utilizzo EF Core.</span><span class="sxs-lookup"><span data-stu-id="2a206-113">This is generally a better choice for EF Core usage.</span></span> <span data-ttu-id="2a206-114">Tuttavia, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> è un caso speciale perché EF Core imposterà i valori generati in entità rilevate dopo il completamento dell'operazione asincrona sul database.</span><span class="sxs-lookup"><span data-stu-id="2a206-114">However, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> is a special case because EF Core will set generated values into tracked entities after the async database operation is complete.</span></span> <span data-ttu-id="2a206-115">Queste modifiche possono quindi attivare le notifiche che, ad esempio, possono essere eseguite in U.I.</span><span class="sxs-lookup"><span data-stu-id="2a206-115">These changes may then trigger notifications which, for example, may have to run on the U.I.</span></span> <span data-ttu-id="2a206-116">thread.</span><span class="sxs-lookup"><span data-stu-id="2a206-116">thread.</span></span> <span data-ttu-id="2a206-117">Di conseguenza, questa modifica viene ripristinata in EF Core 6,0 solo per il <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="2a206-117">Therefore, we are reverting this change in EF Core 6.0 for the <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> method only.</span></span>

### <a name="translate-stringconcat-with-multiple-arguments"></a><span data-ttu-id="2a206-118">Traduci String. Concat con più argomenti</span><span class="sxs-lookup"><span data-stu-id="2a206-118">Translate String.Concat with multiple arguments</span></span>

<span data-ttu-id="2a206-119">Problema di GitHub: [#23859](https://github.com/dotnet/efcore/issues/23859).</span><span class="sxs-lookup"><span data-stu-id="2a206-119">GitHub Issue: [#23859](https://github.com/dotnet/efcore/issues/23859).</span></span> <span data-ttu-id="2a206-120">Questa funzionalità è stata aggiunta come contributo da [@wmeints](https://github.com/wmeints) .</span><span class="sxs-lookup"><span data-stu-id="2a206-120">This feature was contributed by [@wmeints](https://github.com/wmeints).</span></span>

<span data-ttu-id="2a206-121">A partire da EF Core 6,0, le chiamate a <xref:System.String.Concat%2A?displayProperty=nameWithType> con più argomenti vengono ora convertite in SQL.</span><span class="sxs-lookup"><span data-stu-id="2a206-121">Starting with EF Core 6.0, calls to <xref:System.String.Concat%2A?displayProperty=nameWithType> with multiple arguments are now translated to SQL.</span></span> <span data-ttu-id="2a206-122">Ad esempio, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="2a206-122">For example, the following query:</span></span>

<!--
        var shards = context.Shards
            .Where(e => string.Concat(e.Token1, e.Token2, e.Token3) != e.TokensProcessed).ToList();
-->
[!code-csharp[StringConcat](../../../../samples/core/Miscellaneous/NewInEFCore6/StringConcatSample.cs?name=StringConcat)]

<span data-ttu-id="2a206-123">Verrà convertito nel codice SQL seguente quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="2a206-123">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [s].[Id], [s].[Token1], [s].[Token2], [s].[Token3], [s].[TokensProcessed]
FROM [Shards] AS [s]
WHERE ((COALESCE([s].[Token1], N'') + (COALESCE([s].[Token2], N'') + COALESCE([s].[Token3], N''))) <> [s].[TokensProcessed]) OR [s].[TokensProcessed] IS NULL
```

### <a name="smoother-integration-with-systemlinqasync"></a><span data-ttu-id="2a206-124">Integrazione più uniforme con System. Linq. Async</span><span class="sxs-lookup"><span data-stu-id="2a206-124">Smoother integration with System.Linq.Async</span></span>

<span data-ttu-id="2a206-125">Problema di GitHub: [#24041](https://github.com/dotnet/efcore/issues/24041).</span><span class="sxs-lookup"><span data-stu-id="2a206-125">GitHub Issue: [#24041](https://github.com/dotnet/efcore/issues/24041).</span></span>

<span data-ttu-id="2a206-126">Il pacchetto [System. Linq. Async](https://www.nuget.org/packages/System.Linq.Async/) aggiunge l'elaborazione LINQ asincrona sul lato client.</span><span class="sxs-lookup"><span data-stu-id="2a206-126">The [System.Linq.Async](https://www.nuget.org/packages/System.Linq.Async/) package adds client-side async LINQ processing.</span></span> <span data-ttu-id="2a206-127">L'uso di questo pacchetto con le versioni precedenti di EF Core è stato complesso a causa di uno scontro di spazio dei nomi per i metodi LINQ asincroni.</span><span class="sxs-lookup"><span data-stu-id="2a206-127">Using this package with previous versions of EF Core was cumbersome due to a namespace clash for the async LINQ methods.</span></span> <span data-ttu-id="2a206-128">In EF Core 6,0 abbiamo sfruttato i vantaggi dei criteri di ricerca in C# <xref:System.Collections.Generic.IAsyncEnumerable%601> , in modo che il EF Core esposto non <xref:Microsoft.EntityFrameworkCore.DbSet%601> debba implementare direttamente l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="2a206-128">In EF Core 6.0 we have taken advantage of C# pattern matching for <xref:System.Collections.Generic.IAsyncEnumerable%601> such that the exposed EF Core <xref:Microsoft.EntityFrameworkCore.DbSet%601> does not need to implement the interface directly.</span></span>

<span data-ttu-id="2a206-129">Si noti che non è necessario che la maggior parte delle applicazioni usi System. Linq. Async, perché le query EF Core vengono in genere convertite completamente nel server.</span><span class="sxs-lookup"><span data-stu-id="2a206-129">Note that most applications do not need to use System.Linq.Async since EF Core queries are usually fully translated on the server.</span></span>

### <a name="more-flexible-free-text-search"></a><span data-ttu-id="2a206-130">Ricerca di testo libero più flessibile</span><span class="sxs-lookup"><span data-stu-id="2a206-130">More flexible free-text search</span></span>

<span data-ttu-id="2a206-131">Problema di GitHub: [#23921](https://github.com/dotnet/efcore/issues/23921).</span><span class="sxs-lookup"><span data-stu-id="2a206-131">GitHub Issue: [#23921](https://github.com/dotnet/efcore/issues/23921).</span></span>

<span data-ttu-id="2a206-132">In EF Core 6,0 sono stati attenuati i requisiti dei parametri per <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> e <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A> .</span><span class="sxs-lookup"><span data-stu-id="2a206-132">In EF Core 6.0, we have relaxed the parameter requirements for <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> and <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A>.</span></span> <span data-ttu-id="2a206-133">Questo consente di utilizzare queste funzioni con colonne binarie o con colonne mappate mediante un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="2a206-133">This allows these functions to be used with binary columns, or with columns mapped using a value converter.</span></span> <span data-ttu-id="2a206-134">Si consideri, ad esempio, un tipo di entità con una `Name` proprietà definita come oggetto valore:</span><span class="sxs-lookup"><span data-stu-id="2a206-134">For example, consider an entity type with a `Name` property defined as a value object:</span></span>

<!--
    public class Customer
    {
        public int Id { get; set; }

        public Name Name{ get; set; }
    }

    public class Name
    {
        public string First { get; set; }
        public string MiddleInitial { get; set; }
        public string Last { get; set; }
    }
-->
[!code-csharp[EntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=EntityType)]

<span data-ttu-id="2a206-135">Viene eseguito il mapping a JSON nel database:</span><span class="sxs-lookup"><span data-stu-id="2a206-135">This is mapped to JSON in the database:</span></span>

<!--
            modelBuilder.Entity<Customer>()
                .Property(e => e.Name)
                .HasConversion(
                    v => JsonSerializer.Serialize(v, null),
                    v => JsonSerializer.Deserialize<Name>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=ConfigureCompositeValueObject)]

<span data-ttu-id="2a206-136">È ora possibile eseguire una query usando `Contains` o `FreeText` anche se il tipo della proprietà non è `Name` `string` .</span><span class="sxs-lookup"><span data-stu-id="2a206-136">A query can now be executed using `Contains` or `FreeText` even though the type of the property is `Name` not `string`.</span></span> <span data-ttu-id="2a206-137">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-137">For example:</span></span>

<!--
        var result = context.Customers.Where(e => EF.Functions.Contains(e.Name, "Martin")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=Query)]

<span data-ttu-id="2a206-138">Viene generato il codice SQL seguente quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="2a206-138">This generates the following SQL, when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE CONTAINS([c].[Name], N'Martin')
```

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="2a206-139">EF Core 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="2a206-139">EF Core 6.0 Preview 1</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="2a206-140">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="2a206-140">UnicodeAttribute</span></span>

<span data-ttu-id="2a206-141">Problema di GitHub: [#19794](https://github.com/dotnet/efcore/issues/19794).</span><span class="sxs-lookup"><span data-stu-id="2a206-141">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="2a206-142">Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="2a206-142">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="2a206-143">A partire da EF Core 6,0, è ora possibile eseguire il mapping di una proprietà di stringa a una colonna non Unicode usando un attributo di mapping _senza specificare direttamente il tipo di database_.</span><span class="sxs-lookup"><span data-stu-id="2a206-143">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="2a206-144">Si consideri, ad esempio, un `Book` tipo di entità con una proprietà per il [numero di libro standard internazionale (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) nel formato "ISBN 978-3-16-148410-0":</span><span class="sxs-lookup"><span data-stu-id="2a206-144">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

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

<span data-ttu-id="2a206-145">Poiché i codici ISBN non possono contenere caratteri non Unicode, l' `Unicode` attributo provocherà l'uso di un tipo stringa non Unicode.</span><span class="sxs-lookup"><span data-stu-id="2a206-145">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="2a206-146">Inoltre, `MaxLength` viene utilizzato per limitare le dimensioni della colonna di database.</span><span class="sxs-lookup"><span data-stu-id="2a206-146">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="2a206-147">Quando si utilizza SQL Server, ad esempio, viene generata una colonna del database `varchar(22)` :</span><span class="sxs-lookup"><span data-stu-id="2a206-147">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="2a206-148">EF Core esegue il mapping delle proprietà di stringa alle colonne Unicode per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2a206-148">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="2a206-149">`UnicodeAttribute` viene ignorato quando il sistema del database supporta solo tipi Unicode.</span><span class="sxs-lookup"><span data-stu-id="2a206-149">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="2a206-150">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="2a206-150">PrecisionAttribute</span></span>

<span data-ttu-id="2a206-151">Problema di GitHub: [#17914](https://github.com/dotnet/efcore/issues/17914).</span><span class="sxs-lookup"><span data-stu-id="2a206-151">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="2a206-152">Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="2a206-152">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="2a206-153">È ora possibile configurare la precisione e la scala di una colonna di database usando gli attributi di mapping _senza specificare direttamente il tipo di database_.</span><span class="sxs-lookup"><span data-stu-id="2a206-153">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="2a206-154">Si consideri, ad esempio, un `Product` tipo di entità con una `Price` proprietà Decimal:</span><span class="sxs-lookup"><span data-stu-id="2a206-154">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="2a206-155">EF Core eseguirà il mapping di questa proprietà a una colonna del database con precisione 10 e scala 2.</span><span class="sxs-lookup"><span data-stu-id="2a206-155">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="2a206-156">Ad esempio, in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="2a206-156">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="2a206-157">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="2a206-157">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="2a206-158">Problema di GitHub: [#23163](https://github.com/dotnet/efcore/issues/23163).</span><span class="sxs-lookup"><span data-stu-id="2a206-158">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="2a206-159">Questa funzionalità è stata aggiunta come contributo da [@KaloyanIT](https://github.com/KaloyanIT) .</span><span class="sxs-lookup"><span data-stu-id="2a206-159">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="2a206-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> le istanze consentono <xref:Microsoft.EntityFrameworkCore.ModelBuilder> la configurazione di ogni tipo di entità in modo che sia contenuta nella propria classe di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2a206-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="2a206-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-161">For example:</span></span>

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

<span data-ttu-id="2a206-162">In genere, è necessario creare un'istanza di questa classe di configurazione e chiamarla da <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2a206-162">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2a206-163">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-163">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="2a206-164">A partire da EF Core 6,0, un `EntityTypeConfigurationAttribute` può essere inserito nel tipo di entità in modo che EF core possa trovare e usare la configurazione appropriata.</span><span class="sxs-lookup"><span data-stu-id="2a206-164">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="2a206-165">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-165">For example:</span></span>

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

<span data-ttu-id="2a206-166">Questo attributo indica che EF Core utilizzerà l' `IEntityTypeConfiguration` implementazione specificata ogni volta che il `Book` tipo di entità viene incluso in un modello.</span><span class="sxs-lookup"><span data-stu-id="2a206-166">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="2a206-167">Il tipo di entità è incluso in un modello utilizzando uno dei meccanismi normali.</span><span class="sxs-lookup"><span data-stu-id="2a206-167">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="2a206-168">Ad esempio, creando una <xref:Microsoft.EntityFrameworkCore.DbSet%601> proprietà per il tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="2a206-168">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="2a206-169">In alternativa, registrarlo in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> :</span><span class="sxs-lookup"><span data-stu-id="2a206-169">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="2a206-170">`EntityTypeConfigurationAttribute` i tipi non verranno individuati automaticamente in un assembly.</span><span class="sxs-lookup"><span data-stu-id="2a206-170">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="2a206-171">I tipi di entità devono essere aggiunti al modello prima che l'attributo venga individuato in tale tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="2a206-171">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="2a206-172">Translate ToString in SQLite</span><span class="sxs-lookup"><span data-stu-id="2a206-172">Translate ToString on SQLite</span></span>

<span data-ttu-id="2a206-173">Problema di GitHub: [#17223](https://github.com/dotnet/efcore/issues/17223).</span><span class="sxs-lookup"><span data-stu-id="2a206-173">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="2a206-174">Questa funzionalità è stata aggiunta come contributo da [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="2a206-174">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="2a206-175">Le chiamate a <xref:System.Object.ToString> vengono ora convertite in SQL quando si usa il provider di database SQLite.</span><span class="sxs-lookup"><span data-stu-id="2a206-175">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="2a206-176">Questa operazione può essere utile per le ricerche di testo che coinvolgono colonne non di stringa.</span><span class="sxs-lookup"><span data-stu-id="2a206-176">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="2a206-177">Si consideri, ad esempio, un `User` tipo di entità che archivia i numeri di telefono come valori numerici:</span><span class="sxs-lookup"><span data-stu-id="2a206-177">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="2a206-178">`ToString` può essere usato per convertire il numero in una stringa nel database.</span><span class="sxs-lookup"><span data-stu-id="2a206-178">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="2a206-179">È quindi possibile usare questa stringa con una funzione come `LIKE` per trovare i numeri che corrispondono a un modello.</span><span class="sxs-lookup"><span data-stu-id="2a206-179">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="2a206-180">Ad esempio, per trovare tutti i numeri contenenti 555:</span><span class="sxs-lookup"><span data-stu-id="2a206-180">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="2a206-181">Questa operazione viene convertita nel codice SQL seguente quando si usa un database SQLite:</span><span class="sxs-lookup"><span data-stu-id="2a206-181">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="2a206-182">Si noti che la traduzione di <xref:System.Object.ToString> per SQL Server è già supportata in EF Core 5,0 e può essere supportata anche da altri provider di database.</span><span class="sxs-lookup"><span data-stu-id="2a206-182">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="2a206-183">EF. Functions. Random</span><span class="sxs-lookup"><span data-stu-id="2a206-183">EF.Functions.Random</span></span>

<span data-ttu-id="2a206-184">Problema di GitHub: [#16141](https://github.com/dotnet/efcore/issues/16141).</span><span class="sxs-lookup"><span data-stu-id="2a206-184">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="2a206-185">Questa funzionalità è stata aggiunta come contributo da [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="2a206-185">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="2a206-186">`EF.Functions.Random` esegue il mapping a una funzione di database che restituisce un numero pseudo-casuale compreso tra 0 e 1 esclusivo.</span><span class="sxs-lookup"><span data-stu-id="2a206-186">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="2a206-187">Le traduzioni sono state implementate nel repository EF Core per SQL Server, SQLite e Cosmos.</span><span class="sxs-lookup"><span data-stu-id="2a206-187">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="2a206-188">Si consideri, ad esempio, un `User` tipo di entità con una `Popularity` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2a206-188">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="2a206-189">`Popularity` i valori possono essere compresi tra 1 e 5.</span><span class="sxs-lookup"><span data-stu-id="2a206-189">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="2a206-190">Utilizzando `EF.Functions.Random` è possibile scrivere una query per restituire tutti gli utenti con una popolarità scelta in modo casuale:</span><span class="sxs-lookup"><span data-stu-id="2a206-190">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="2a206-191">In questo modo viene convertito nel codice SQL seguente quando si utilizza un database di SQL Server:</span><span class="sxs-lookup"><span data-stu-id="2a206-191">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="2a206-192">Supporto per SQL Server colonne di tipo sparse</span><span class="sxs-lookup"><span data-stu-id="2a206-192">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="2a206-193">Problema di GitHub: [#8023](https://github.com/dotnet/efcore/issues/8023).</span><span class="sxs-lookup"><span data-stu-id="2a206-193">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="2a206-194">SQL Server [colonne di tipo sparse](/sql/relational-databases/tables/use-sparse-columns) sono colonne ordinarie ottimizzate per l'archiviazione di valori null.</span><span class="sxs-lookup"><span data-stu-id="2a206-194">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="2a206-195">Questa operazione può essere utile quando si utilizza il [mapping di ereditarietà TPH](xref:core/modeling/inheritance) in cui le proprietà di un sottotipo raramente utilizzato determineranno valori di colonna null per la maggior parte delle righe della tabella.</span><span class="sxs-lookup"><span data-stu-id="2a206-195">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="2a206-196">Si consideri, ad esempio, una `ForumModerator` classe che si estende da `ForumUser` :</span><span class="sxs-lookup"><span data-stu-id="2a206-196">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

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

<span data-ttu-id="2a206-197">Potrebbero essere presenti milioni di utenti, solo alcuni dei quali sono moderatori.</span><span class="sxs-lookup"><span data-stu-id="2a206-197">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="2a206-198">Questo significa che il mapping `ForumName` di come sparse potrebbe avere senso qui.</span><span class="sxs-lookup"><span data-stu-id="2a206-198">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="2a206-199">A questo punto è possibile configurare usando `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="2a206-199">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="2a206-200">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-200">For example:</span></span>

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

<span data-ttu-id="2a206-201">EF Core le migrazioni contrassegnano quindi la colonna come sparse.</span><span class="sxs-lookup"><span data-stu-id="2a206-201">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="2a206-202">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-202">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="2a206-203">Le colonne di tipo sparse presentano limitazioni.</span><span class="sxs-lookup"><span data-stu-id="2a206-203">Sparse columns have limitations.</span></span> <span data-ttu-id="2a206-204">Assicurarsi di leggere la [documentazione relativa alle colonne di tipo sparse SQL Server](/sql/relational-databases/tables/use-sparse-columns) per assicurarsi che le colonne di tipo sparse siano la scelta migliore per lo scenario.</span><span class="sxs-lookup"><span data-stu-id="2a206-204">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="2a206-205">Database in memoria: le proprietà richieste Validate non sono null</span><span class="sxs-lookup"><span data-stu-id="2a206-205">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="2a206-206">Problema di GitHub: [#10613](https://github.com/dotnet/efcore/issues/10613).</span><span class="sxs-lookup"><span data-stu-id="2a206-206">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="2a206-207">Questa funzionalità è stata aggiunta come contributo da [@fagnercarvalho](https://github.com/fagnercarvalho) .</span><span class="sxs-lookup"><span data-stu-id="2a206-207">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="2a206-208">Il EF Core database in memoria genera ora un'eccezione se viene effettuato un tentativo di salvare un valore null per una proprietà contrassegnata come obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="2a206-208">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="2a206-209">Si consideri, ad esempio, un `User` tipo con una `Username` proprietà obbligatoria:</span><span class="sxs-lookup"><span data-stu-id="2a206-209">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="2a206-210">Se si tenta di salvare un'entità con un valore null `Username` , verrà generata l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="2a206-210">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="2a206-211">Microsoft. EntityFrameworkCore. eccezione dbupdateexception: mancano le proprietà obbligatorie ' {' username '}' per l'istanza del tipo di entità' User ' con il valore di chiave ' {ID: 1}'.</span><span class="sxs-lookup"><span data-stu-id="2a206-211">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="2a206-212">Questa convalida può essere disabilitata, se necessario.</span><span class="sxs-lookup"><span data-stu-id="2a206-212">This validation can be disabled if necessary.</span></span> <span data-ttu-id="2a206-213">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-213">For example:</span></span>

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="2a206-214">Traduzione SQL Server migliorata per IsNullOrWhitespace</span><span class="sxs-lookup"><span data-stu-id="2a206-214">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="2a206-215">Problema di GitHub: [#22916](https://github.com/dotnet/efcore/issues/22916).</span><span class="sxs-lookup"><span data-stu-id="2a206-215">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="2a206-216">Questa funzionalità è stata aggiunta come contributo da [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="2a206-216">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="2a206-217">Si consideri la query seguente:</span><span class="sxs-lookup"><span data-stu-id="2a206-217">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="2a206-218">Prima di EF Core 6,0, questa operazione è stata convertita nel SQL Server seguente:</span><span class="sxs-lookup"><span data-stu-id="2a206-218">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="2a206-219">Questa traduzione è stata migliorata per EF Core 6,0 a:</span><span class="sxs-lookup"><span data-stu-id="2a206-219">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="2a206-220">I commenti del database sono con ponteggi ai commenti del codice</span><span class="sxs-lookup"><span data-stu-id="2a206-220">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="2a206-221">Problema di GitHub: [#19113](https://github.com/dotnet/efcore/issues/19113).</span><span class="sxs-lookup"><span data-stu-id="2a206-221">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="2a206-222">Questa funzionalità è stata aggiunta come contributo da [@ErikEJ](https://github.com/ErikEJ) .</span><span class="sxs-lookup"><span data-stu-id="2a206-222">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="2a206-223">I commenti sulle tabelle e sulle colonne SQL sono ora sottoposte a impalcatura nei tipi di entità creati durante la [decompilazione di un modello di EF Core](xref:core/managing-schemas/scaffolding) da un database SQL Server esistente.</span><span class="sxs-lookup"><span data-stu-id="2a206-223">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="2a206-224">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2a206-224">For example:</span></span>

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

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="2a206-225">Microsoft. Data. sqlite 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="2a206-225">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="2a206-226">È possibile eseguire ed eseguire il debug in tutti gli esempi di anteprima 1 mostrati di seguito [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="2a206-226">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="2a206-227">API salvataggio</span><span class="sxs-lookup"><span data-stu-id="2a206-227">Savepoints API</span></span>

<span data-ttu-id="2a206-228">Problema di GitHub: [#20228](https://github.com/dotnet/efcore/issues/20228).</span><span class="sxs-lookup"><span data-stu-id="2a206-228">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="2a206-229">È stata standardizzata [un'API comune per salvataggio nei provider ADO.NET](https://github.com/dotnet/runtime/issues/33397).</span><span class="sxs-lookup"><span data-stu-id="2a206-229">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="2a206-230">Microsoft. Data. sqlite ora supporta questa API, tra cui:</span><span class="sxs-lookup"><span data-stu-id="2a206-230">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="2a206-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> per creare un salvataggio nella transazione</span><span class="sxs-lookup"><span data-stu-id="2a206-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="2a206-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> per eseguire il rollback a una salvataggio precedente</span><span class="sxs-lookup"><span data-stu-id="2a206-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="2a206-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> per rilasciare un salvataggio</span><span class="sxs-lookup"><span data-stu-id="2a206-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="2a206-234">L'uso di un salvataggio consente di eseguire il rollback di parte di una transazione senza eseguire il rollback dell'intera transazione.</span><span class="sxs-lookup"><span data-stu-id="2a206-234">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="2a206-235">Ad esempio, il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2a206-235">For example, the code below:</span></span>

- <span data-ttu-id="2a206-236">Crea una transazione</span><span class="sxs-lookup"><span data-stu-id="2a206-236">Creates a transaction</span></span>
- <span data-ttu-id="2a206-237">Invia un aggiornamento al database</span><span class="sxs-lookup"><span data-stu-id="2a206-237">Sends an update to the database</span></span>
- <span data-ttu-id="2a206-238">Crea un salvataggio</span><span class="sxs-lookup"><span data-stu-id="2a206-238">Creates a savepoint</span></span>
- <span data-ttu-id="2a206-239">Invia un altro aggiornamento al database</span><span class="sxs-lookup"><span data-stu-id="2a206-239">Sends another update to the database</span></span>
- <span data-ttu-id="2a206-240">Esegue il rollback al salvataggio creato in precedenza</span><span class="sxs-lookup"><span data-stu-id="2a206-240">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="2a206-241">Esegui il commit della transazione</span><span class="sxs-lookup"><span data-stu-id="2a206-241">Commits the transaction</span></span>

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

<span data-ttu-id="2a206-242">Questo comporterà il commit del primo aggiornamento nel database, mentre non viene eseguito il commit del secondo aggiornamento dal momento in cui è stato eseguito il rollback di salvataggio prima di eseguire il commit della transazione.</span><span class="sxs-lookup"><span data-stu-id="2a206-242">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="2a206-243">Timeout del comando nella stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="2a206-243">Command timeout in the connection string</span></span>

<span data-ttu-id="2a206-244">Problema di GitHub: [#22505](https://github.com/dotnet/efcore/issues/22505).</span><span class="sxs-lookup"><span data-stu-id="2a206-244">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="2a206-245">Questa funzionalità è stata aggiunta come contributo da [@nmichels](https://github.com/nmichels) .</span><span class="sxs-lookup"><span data-stu-id="2a206-245">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="2a206-246">I provider ADO.NET supportano due timeout distinti:</span><span class="sxs-lookup"><span data-stu-id="2a206-246">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="2a206-247">Timeout della connessione, che determina il tempo massimo di attesa durante l'esecuzione di una connessione al database.</span><span class="sxs-lookup"><span data-stu-id="2a206-247">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="2a206-248">Timeout del comando, che determina il tempo massimo di attesa per il completamento dell'esecuzione di un comando.</span><span class="sxs-lookup"><span data-stu-id="2a206-248">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="2a206-249">Il timeout del comando può essere impostato dal codice usando <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2a206-249">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2a206-250">Molti provider espongono ora questo timeout del comando nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="2a206-250">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="2a206-251">Microsoft. Data. sqlite segue questa tendenza con la `Command Timeout` parola chiave della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="2a206-251">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="2a206-252">Ad esempio, `"Command Timeout=60;DataSource=test.db"` utilizzerà 60 secondi come timeout predefinito per i comandi creati dalla connessione.</span><span class="sxs-lookup"><span data-stu-id="2a206-252">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="2a206-253">SQLite considera `Default Timeout` come sinonimo di `Command Timeout` e pertanto può essere usato in alternativa se si preferisce.</span><span class="sxs-lookup"><span data-stu-id="2a206-253">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
