---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e52b77e8c19ab3005aee50e1cf4e170d3ee1f502
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023445"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="2d3ca-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="2d3ca-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="2d3ca-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="2d3ca-105">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="2d3ca-105">Summary</span></span>

| <span data-ttu-id="2d3ca-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="2d3ca-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="2d3ca-108">EF Core 5,0 non supporta .NET Framework</span><span class="sxs-lookup"><span data-stu-id="2d3ca-108">EF Core 5.0 does not support .NET Framework</span></span>](#netstandard21)                                                                         | <span data-ttu-id="2d3ca-109">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-109">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-110">IProperty. getColumnName () è obsoleto</span><span class="sxs-lookup"><span data-stu-id="2d3ca-110">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="2d3ca-111">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-111">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-112">La precisione e la scala sono necessarie per i numeri decimali</span><span class="sxs-lookup"><span data-stu-id="2d3ca-112">Precision and scale are required for decimals</span></span>](#decimals)                                                                            | <span data-ttu-id="2d3ca-113">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-113">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-114">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="2d3ca-114">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="2d3ca-115">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-115">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-116">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="2d3ca-116">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="2d3ca-117">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-117">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-118">Le esplorazioni di riferimento non null non vengono sovrascritte dalle query</span><span class="sxs-lookup"><span data-stu-id="2d3ca-118">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="2d3ca-119">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-119">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-120">Toview () viene considerato in modo diverso dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="2d3ca-120">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="2d3ca-121">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-121">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-122">ToTable (null) contrassegna il tipo di entità come non mappato a una tabella</span><span class="sxs-lookup"><span data-stu-id="2d3ca-122">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="2d3ca-123">Medio</span><span class="sxs-lookup"><span data-stu-id="2d3ca-123">Medium</span></span>     |
| [<span data-ttu-id="2d3ca-124">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="2d3ca-124">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="2d3ca-125">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-125">Low</span></span>        |
| [<span data-ttu-id="2d3ca-126">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="2d3ca-126">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="2d3ca-127">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-127">Low</span></span>        |
| [<span data-ttu-id="2d3ca-128">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="2d3ca-128">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="2d3ca-129">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-129">Low</span></span>        |
| <span data-ttu-id="2d3ca-130">[Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="2d3ca-130">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="2d3ca-131">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-131">Low</span></span>        |
| [<span data-ttu-id="2d3ca-132">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="2d3ca-132">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="2d3ca-133">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-133">Low</span></span>        |
| [<span data-ttu-id="2d3ca-134">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="2d3ca-134">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="2d3ca-135">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-135">Low</span></span>        |
| [<span data-ttu-id="2d3ca-136">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2d3ca-136">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="2d3ca-137">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-137">Low</span></span>        |
| [<span data-ttu-id="2d3ca-138">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="2d3ca-138">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="2d3ca-139">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-139">Low</span></span>        |
| [<span data-ttu-id="2d3ca-140">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="2d3ca-140">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="2d3ca-141">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-141">Low</span></span>        |
| [<span data-ttu-id="2d3ca-142">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="2d3ca-142">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="2d3ca-143">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-143">Low</span></span>        |
| [<span data-ttu-id="2d3ca-144">Un pluralizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="2d3ca-144">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="2d3ca-145">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-145">Low</span></span>        |
| [<span data-ttu-id="2d3ca-146">INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip</span><span class="sxs-lookup"><span data-stu-id="2d3ca-146">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="2d3ca-147">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-147">Low</span></span>        |
| [<span data-ttu-id="2d3ca-148">Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate</span><span class="sxs-lookup"><span data-stu-id="2d3ca-148">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="2d3ca-149">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-149">Low</span></span>        |
| [<span data-ttu-id="2d3ca-150">L'uso di una raccolta di tipi queryable nella proiezione non è supportato</span><span class="sxs-lookup"><span data-stu-id="2d3ca-150">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="2d3ca-151">Basso</span><span class="sxs-lookup"><span data-stu-id="2d3ca-151">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="2d3ca-152">Modifiche a media Impact</span><span class="sxs-lookup"><span data-stu-id="2d3ca-152">Medium-impact changes</span></span>

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a><span data-ttu-id="2d3ca-153">EF Core 5,0 non supporta .NET Framework</span><span class="sxs-lookup"><span data-stu-id="2d3ca-153">EF Core 5.0 does not support .NET Framework</span></span>

[<span data-ttu-id="2d3ca-154">Problema n. 15498</span><span class="sxs-lookup"><span data-stu-id="2d3ca-154">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-155">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-155">Old behavior</span></span>

<span data-ttu-id="2d3ca-156">EF Core 3,1 destinazioni .NET Standard 2,0, supportato da .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-156">EF Core 3.1 targets .NET Standard 2.0, which is supported by .NET Framework.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-157">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-157">New behavior</span></span>

<span data-ttu-id="2d3ca-158">EF Core 5,0 destinazioni .NET Standard 2,1, che non è supportato da .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-158">EF Core 5.0 targets .NET Standard 2.1, which is not supported by .NET Framework.</span></span> <span data-ttu-id="2d3ca-159">Ciò significa EF Core 5,0 non può essere utilizzato con .NET Framework applicazioni.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-159">This means EF Core 5.0 cannot be used with .NET Framework applications.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-160">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-160">Why</span></span>

<span data-ttu-id="2d3ca-161">Questa operazione fa parte dello spostamento più ampio tra i team .NET finalizzati all'unificazione di un singolo Framework di destinazione .NET.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-161">This is part of the wider movement across .NET teams aimed at unification to a single .NET target framework.</span></span> <span data-ttu-id="2d3ca-162">Per ulteriori informazioni, vedere [il futuro del .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-162">For more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-163">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-163">Mitigations</span></span>

<span data-ttu-id="2d3ca-164">.NET Framework applicazioni possono continuare a usare EF Core 3,1, che è una [versione di supporto a lungo termine (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-164">.NET Framework applications can continue to use EF Core 3.1, which is a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="2d3ca-165">In alternativa, è possibile aggiornare le applicazioni per l'uso di .NET Core 3,1 o .NET 5, che supportano entrambe .NET Standard 2,1.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-165">Alternately, applications can be updated to use .NET Core 3.1 or .NET 5, both of which support .NET Standard 2.1.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="2d3ca-166">IProperty. getColumnName () è obsoleto</span><span class="sxs-lookup"><span data-stu-id="2d3ca-166">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="2d3ca-167">Rilevamento del problema #2266</span><span class="sxs-lookup"><span data-stu-id="2d3ca-167">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-168">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-168">Old behavior</span></span>

<span data-ttu-id="2d3ca-169">`GetColumnName()` Restituisce il nome della colonna a cui è stato eseguito il mapping di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-169">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-170">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-170">New behavior</span></span>

<span data-ttu-id="2d3ca-171">`GetColumnName()` restituisce comunque il nome di una colonna a cui è stato eseguito il mapping di una proprietà, ma questo comportamento è ambiguo perché EF Core 5 supporta TPT e il mapping simultaneo a una vista o a una funzione in cui questi mapping potrebbero utilizzare nomi di colonna diversi per la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-171">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-172">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-172">Why</span></span>

<span data-ttu-id="2d3ca-173">Questo metodo è stato contrassegnato come obsoleto per guidare gli utenti a un sovraccarico più accurato <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-173">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-174">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-174">Mitigations</span></span>

<span data-ttu-id="2d3ca-175">Usare il codice seguente per ottenere il nome della colonna per una tabella specifica:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-175">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a><span data-ttu-id="2d3ca-176">La precisione e la scala sono necessarie per i numeri decimali</span><span class="sxs-lookup"><span data-stu-id="2d3ca-176">Precision and scale are required for decimals</span></span>

[<span data-ttu-id="2d3ca-177">Rilevamento del problema #19293</span><span class="sxs-lookup"><span data-stu-id="2d3ca-177">Tracking Issue #19293</span></span>](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-178">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-178">Old behavior</span></span>

<span data-ttu-id="2d3ca-179">EF Core in genere non ha impostato la precisione e la scala sugli <xref:Microsoft.Data.SqlClient.SqlParameter> oggetti.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-179">EF Core did not normally set precision and scale on <xref:Microsoft.Data.SqlClient.SqlParameter> objects.</span></span> <span data-ttu-id="2d3ca-180">Ciò significa che la precisione e la scalabilità completa sono state inviate a SQL Server, a quel punto SQL Server arrotondate in base alla precisione e alla scala della colonna del database.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-180">This means the full precision and scale was sent to SQL Server, at which point SQL Server would round based on the precision and scale of the database column.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-181">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-181">New behavior</span></span>

<span data-ttu-id="2d3ca-182">EF Core ora imposta la precisione e la scalabilità sui parametri usando i valori configurati per le proprietà nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-182">EF Core now sets precision and scale on parameters using the values configured for properties in the EF Core model.</span></span> <span data-ttu-id="2d3ca-183">Ciò significa che l'arrotondamento si verifica ora in SqlClient.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-183">This means rounding now happens in SqlClient.</span></span> <span data-ttu-id="2d3ca-184">In modo consequenziale, se la precisione e la scala configurate non corrispondono alla precisione e alla scala del database, l'arrotondamento visualizzato potrebbe cambiare.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-184">Consequentially, if the configured precision and scale do not match the database precision and scale, then the rounding seen may change.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-185">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-185">Why</span></span>

<span data-ttu-id="2d3ca-186">Le nuove funzionalità di SQL Server, tra cui Always Encrypted, richiedono che i facet di parametro siano specificati in modo completo.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-186">Newer SQL Server features, including Always Encrypted, require that parameter facets are fully specified.</span></span> <span data-ttu-id="2d3ca-187">Inoltre, SqlClient ha apportato una modifica a Round anziché troncare i valori decimali, in modo che corrisponda al comportamento del SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-187">In addition, SqlClient made a change to round instead of truncate decimal values, thereby matching the SQL Server behavior.</span></span> <span data-ttu-id="2d3ca-188">In questo modo è possibile EF Core impostare questi facet senza modificare il comportamento per i numeri decimali configurati correttamente.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-188">This made it possible for EF Core to set these facets without changing the behavior for correctly configured decimals.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-189">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-189">Mitigations</span></span>

<span data-ttu-id="2d3ca-190">Eseguire il mapping delle proprietà Decimal usando un nome di tipo che include precisione e scala.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-190">Map your decimal properties using a type name that includes precision and scale.</span></span> <span data-ttu-id="2d3ca-191">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-191">For example:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

<span data-ttu-id="2d3ca-192">Oppure usare `HasPrecision` nelle API per la creazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-192">Or use `HasPrecision` in the model building APIs.</span></span> <span data-ttu-id="2d3ca-193">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-193">For example:</span></span>

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="2d3ca-194">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="2d3ca-194">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="2d3ca-195">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="2d3ca-195">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-196">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-196">Old behavior</span></span>

<span data-ttu-id="2d3ca-197">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-197">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="2d3ca-198">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-198">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-199">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-199">New behavior</span></span>

<span data-ttu-id="2d3ca-200">Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-200">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="2d3ca-201">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-201">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="2d3ca-202">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-202">Why</span></span>

<span data-ttu-id="2d3ca-203">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-203">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-204">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-204">Mitigations</span></span>

<span data-ttu-id="2d3ca-205">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="2d3ca-205">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="2d3ca-206">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="2d3ca-206">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="2d3ca-207">Rilevamento del problema #18903</span><span class="sxs-lookup"><span data-stu-id="2d3ca-207">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-208">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-208">Old behavior</span></span>

<span data-ttu-id="2d3ca-209">È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-209">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="2d3ca-210">Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-210">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-211">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-211">New behavior</span></span>

<span data-ttu-id="2d3ca-212">Le API per la definizione della query sono deprecate.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-212">APIs for defining query are deprecated.</span></span> <span data-ttu-id="2d3ca-213">Sono state introdotte nuove API specifiche del provider.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-213">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-214">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-214">Why</span></span>

<span data-ttu-id="2d3ca-215">Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-215">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="2d3ca-216">Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-216">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="2d3ca-217">Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-217">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="2d3ca-218">Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider In-Memory per le entità senza chiave (analogamente alle viste di database nei database relazionali).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-218">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="2d3ca-219">Questa definizione consente di testare facilmente l'applicazione in base al database in memoria.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-219">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="2d3ca-220">In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-220">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="2d3ca-221">Quindi abbiamo deciso di semplificare il concetto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-221">So we decided to simplify the concept.</span></span> <span data-ttu-id="2d3ca-222">È stata creata una query di definizione basata su LINQ esclusiva per In-Memory provider e gestirli in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-222">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="2d3ca-223">Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-223">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-224">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-224">Mitigations</span></span>

<span data-ttu-id="2d3ca-225">Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-225">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="2d3ca-226">Per il provider di In-Memory, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-226">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="2d3ca-227">Le esplorazioni di riferimento non null non vengono sovrascritte dalle query</span><span class="sxs-lookup"><span data-stu-id="2d3ca-227">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="2d3ca-228">Rilevamento del problema #2693</span><span class="sxs-lookup"><span data-stu-id="2d3ca-228">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-229">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-229">Old behavior</span></span>

<span data-ttu-id="2d3ca-230">In EF Core 3,1, le esplorazioni dei riferimenti inizializzate in modo eager con valori non null verrebbero talvolta sovrascritte dalle istanze di entità dal database, indipendentemente dal fatto che i valori delle chiavi corrispondano o meno.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-230">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="2d3ca-231">Tuttavia, in altri casi, EF Core 3,1 esegue l'operazione opposta e lascia il valore non null esistente.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-231">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-232">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-232">New behavior</span></span>

<span data-ttu-id="2d3ca-233">A partire da EF Core 5,0, le navigazioni di riferimento non null non vengono mai sovrascritte dalle istanze restituite da una query.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-233">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="2d3ca-234">Si noti che l'inizializzazione eager di una _raccolta_ di spostamento in una raccolta vuota è ancora supportata.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-234">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-235">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-235">Why</span></span>

<span data-ttu-id="2d3ca-236">L'inizializzazione di una proprietà di navigazione di riferimento su un'istanza di entità "vuota" restituisce uno stato ambiguo.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-236">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="2d3ca-237">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-237">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="2d3ca-238">In genere, una query per i Blog e gli autori creerà prima di tutto le `Blog` istanze e quindi imposterà le `Author` istanze appropriate in base ai dati restituiti dal database.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-238">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="2d3ca-239">Tuttavia, in questo caso ogni `Blog.Author` proprietà è già inizializzata su un oggetto vuoto `Author` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-239">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="2d3ca-240">Ad eccezione di EF Core non è possibile verificare che l'istanza sia "vuota".</span><span class="sxs-lookup"><span data-stu-id="2d3ca-240">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="2d3ca-241">Quindi, sovrascrivendo questa istanza potrebbe essere possibile eliminare un valore valido in modo invisibile all'utente `Author` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-241">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="2d3ca-242">Pertanto, EF Core 5,0 ora non sovrascrive in modo coerente una navigazione già inizializzata.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-242">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="2d3ca-243">Questo nuovo comportamento è anche allineato con il comportamento di EF6 nella maggior parte dei casi, sebbene durante l'analisi sono stati rilevati anche alcuni casi di incoerenza in EF6.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-243">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-244">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-244">Mitigations</span></span>

<span data-ttu-id="2d3ca-245">Se viene rilevata questa interruzione, la correzione consiste nell'arrestare l'inizializzazione delle proprietà di navigazione dei riferimenti.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-245">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="2d3ca-246">Toview () viene considerato in modo diverso dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="2d3ca-246">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="2d3ca-247">Rilevamento del problema #2725</span><span class="sxs-lookup"><span data-stu-id="2d3ca-247">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-248">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-248">Old behavior</span></span>

<span data-ttu-id="2d3ca-249">La chiamata a `ToView(string)` ha fatto in modo che le migrazioni ignorino il tipo di entità oltre a eseguire il mapping a una vista.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-249">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-250">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-250">New behavior</span></span>

<span data-ttu-id="2d3ca-251">Ora `ToView(string)` contrassegna il tipo di entità come non mappato a una tabella, oltre a eseguirne il mapping a una vista.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-251">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="2d3ca-252">Ciò comporta la prima migrazione dopo l'aggiornamento a EF Core 5 per tentare di eliminare la tabella predefinita per questo tipo di entità perché non viene più ignorata.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-252">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-253">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-253">Why</span></span>

<span data-ttu-id="2d3ca-254">EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, pertanto `ToView` non è più un indicatore valido che deve essere ignorato dalle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-254">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-255">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-255">Mitigations</span></span>

<span data-ttu-id="2d3ca-256">Usare il codice seguente per contrassegnare la tabella mappata come esclusa dalle migrazioni:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-256">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="2d3ca-257">ToTable (null) contrassegna il tipo di entità come non mappato a una tabella</span><span class="sxs-lookup"><span data-stu-id="2d3ca-257">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="2d3ca-258">Rilevamento del problema #21172</span><span class="sxs-lookup"><span data-stu-id="2d3ca-258">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-259">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-259">Old behavior</span></span>

<span data-ttu-id="2d3ca-260">`ToTable(null)` Reimposta il nome della tabella sul valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-260">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-261">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-261">New behavior</span></span>

<span data-ttu-id="2d3ca-262">`ToTable(null)` ora contrassegna il tipo di entità come non mappato ad alcuna tabella.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-262">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-263">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-263">Why</span></span>

<span data-ttu-id="2d3ca-264">EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, quindi `ToTable(null)` viene usato per indicare che non è mappato ad alcuna tabella.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-264">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-265">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-265">Mitigations</span></span>

<span data-ttu-id="2d3ca-266">Usare il codice seguente per reimpostare il nome della tabella sul valore predefinito se non è mappato a una vista o a un DbFunction:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-266">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a><span data-ttu-id="2d3ca-267">Modifiche a basso effetto</span><span class="sxs-lookup"><span data-stu-id="2d3ca-267">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="2d3ca-268">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="2d3ca-268">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="2d3ca-269">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="2d3ca-269">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-270">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-270">Old behavior</span></span>

<span data-ttu-id="2d3ca-271">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-271">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="2d3ca-272">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-272">However, it only ever affected database creation.</span></span> <span data-ttu-id="2d3ca-273">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-273">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="2d3ca-274">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="2d3ca-274">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-275">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-275">New behavior</span></span>

<span data-ttu-id="2d3ca-276">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-276">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="2d3ca-277">Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-277">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-278">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-278">Why</span></span>

<span data-ttu-id="2d3ca-279">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-279">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-280">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-280">Mitigations</span></span>

<span data-ttu-id="2d3ca-281">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-281">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="2d3ca-282">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="2d3ca-282">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="2d3ca-283">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="2d3ca-283">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-284">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-284">Old behavior</span></span>

<span data-ttu-id="2d3ca-285">La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-285">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-286">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-286">New behavior</span></span>

<span data-ttu-id="2d3ca-287">La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-287">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-288">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-288">Why</span></span>

<span data-ttu-id="2d3ca-289">Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-289">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-290">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-290">Mitigations</span></span>

<span data-ttu-id="2d3ca-291">Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-291">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="2d3ca-292">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="2d3ca-292">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="2d3ca-293">Rilevamento del problema #17751</span><span class="sxs-lookup"><span data-stu-id="2d3ca-293">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-294">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-294">Old behavior</span></span>

<span data-ttu-id="2d3ca-295">Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-295">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-296">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-296">New behavior</span></span>

<span data-ttu-id="2d3ca-297">La proprietà shadow creata per convenzione è ora denominata `__id` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-297">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-298">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-298">Why</span></span>

<span data-ttu-id="2d3ca-299">Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-299">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-300">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-300">Mitigations</span></span>

<span data-ttu-id="2d3ca-301">Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-301">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="2d3ca-302">Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri</span><span class="sxs-lookup"><span data-stu-id="2d3ca-302">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="2d3ca-303">Rilevamento del problema #17306</span><span class="sxs-lookup"><span data-stu-id="2d3ca-303">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-304">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-304">Old behavior</span></span>

<span data-ttu-id="2d3ca-305">Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-305">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-306">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-306">New behavior</span></span>

<span data-ttu-id="2d3ca-307">Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-307">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-308">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-308">Why</span></span>

<span data-ttu-id="2d3ca-309">Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-309">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-310">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-310">Mitigations</span></span>

<span data-ttu-id="2d3ca-311">I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-311">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="2d3ca-312">Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/dotnet/efcore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="2d3ca-312">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="2d3ca-313">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="2d3ca-313">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="2d3ca-314">Rilevamento del problema #17874</span><span class="sxs-lookup"><span data-stu-id="2d3ca-314">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-315">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-315">Old behavior</span></span>

<span data-ttu-id="2d3ca-316">In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2d3ca-316">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-317">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-317">New behavior</span></span>

<span data-ttu-id="2d3ca-318">L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2d3ca-318">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-319">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-319">Why</span></span>

<span data-ttu-id="2d3ca-320">Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-320">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-321">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-321">Mitigations</span></span>

<span data-ttu-id="2d3ca-322">Usare la nuova API.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-322">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="2d3ca-323">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="2d3ca-323">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="2d3ca-324">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="2d3ca-324">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-325">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-325">Old behavior</span></span>

<span data-ttu-id="2d3ca-326">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-326">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-327">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-327">New behavior</span></span>

<span data-ttu-id="2d3ca-328">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-328">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-329">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-329">Why</span></span>

<span data-ttu-id="2d3ca-330">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-330">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-331">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-331">Mitigations</span></span>

<span data-ttu-id="2d3ca-332">Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-332">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="2d3ca-333">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2d3ca-333">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="2d3ca-334">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="2d3ca-334">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-335">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-335">Old behavior</span></span>

<span data-ttu-id="2d3ca-336">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-336">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-337">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-337">New behavior</span></span>

<span data-ttu-id="2d3ca-338">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-338">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="2d3ca-339">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-339">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-340">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-340">Why</span></span>

<span data-ttu-id="2d3ca-341">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-341">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="2d3ca-342">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-342">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-343">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-343">Mitigations</span></span>

<span data-ttu-id="2d3ca-344">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="2d3ca-344">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="2d3ca-345">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="2d3ca-345">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="2d3ca-346">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="2d3ca-346">Discriminators are read-only</span></span>

[<span data-ttu-id="2d3ca-347">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="2d3ca-347">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-348">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-348">Old behavior</span></span>

<span data-ttu-id="2d3ca-349">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="2d3ca-349">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-350">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-350">New behavior</span></span>

<span data-ttu-id="2d3ca-351">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-351">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-352">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-352">Why</span></span>

<span data-ttu-id="2d3ca-353">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-353">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-354">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-354">Mitigations</span></span>

<span data-ttu-id="2d3ca-355">Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-355">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="2d3ca-356">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="2d3ca-356">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="2d3ca-357">Rilevamento del problema #20294</span><span class="sxs-lookup"><span data-stu-id="2d3ca-357">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-358">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-358">Old behavior</span></span>

<span data-ttu-id="2d3ca-359">EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-359">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="2d3ca-360">Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-360">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-361">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-361">New behavior</span></span>

<span data-ttu-id="2d3ca-362">I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-362">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-363">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-363">Why</span></span>

<span data-ttu-id="2d3ca-364">I metodi specifici del provider vengono mappati a una funzione di database.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-364">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="2d3ca-365">Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-365">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="2d3ca-366">È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-366">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="2d3ca-367">Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-367">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="2d3ca-368">Poiché il provider InMemory è un *database* diverso, questi metodi non sono disponibili per questo provider.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-368">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="2d3ca-369">Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-369">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-370">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-370">Mitigations</span></span>

<span data-ttu-id="2d3ca-371">Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-371">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="2d3ca-372">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="2d3ca-372">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="2d3ca-373">Rilevamento del problema #21089</span><span class="sxs-lookup"><span data-stu-id="2d3ca-373">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-374">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-374">Old behavior</span></span>

<span data-ttu-id="2d3ca-375">In precedenza era possibile definire un solo indice su un determinato set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-375">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="2d3ca-376">Il nome del database di un indice è stato configurato con IndexBuilder. HasName.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-376">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-377">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-377">New behavior</span></span>

<span data-ttu-id="2d3ca-378">Sono ora consentiti più indici nello stesso set o proprietà.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-378">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="2d3ca-379">Questi indici sono ora distinti da un nome nel modello.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-379">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="2d3ca-380">Per convenzione, il nome del modello viene utilizzato come nome del database. Tuttavia, può anche essere configurato in modo indipendente utilizzando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-380">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-381">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-381">Why</span></span>

<span data-ttu-id="2d3ca-382">In futuro, vorremmo consentire l'abilitazione di indici sia ascendenti che decrescenti con regole di confronto diverse nello stesso set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-382">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="2d3ca-383">Questa modifica consente di spostarsi in un altro passaggio della direzione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-383">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-384">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-384">Mitigations</span></span>

<span data-ttu-id="2d3ca-385">Il codice precedentemente chiamato IndexBuilder. HasName deve essere aggiornato in modo da chiamare HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-385">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="2d3ca-386">Se il progetto include migrazioni generate prima della versione 2.0.0 di EF Core, è possibile ignorare l'avviso in tali file ed eliminarlo aggiungendo `#pragma warning disable 612, 618` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-386">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="2d3ca-387">Un pluralizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="2d3ca-387">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="2d3ca-388">Rilevamento del problema #11160</span><span class="sxs-lookup"><span data-stu-id="2d3ca-388">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-389">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-389">Old behavior</span></span>

<span data-ttu-id="2d3ca-390">In precedenza era necessario installare un pacchetto pluralizer separato per plurali DbSet e i nomi di navigazione della raccolta e i nomi di tabella singolari durante l'impalcatura di un DbContext e i tipi di entità per reverse engineering uno schema di database.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-390">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-391">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-391">New behavior</span></span>

<span data-ttu-id="2d3ca-392">EF Core ora include un pluralizer che usa la libreria [umanizzator](https://github.com/Humanizr/Humanizer) .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-392">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="2d3ca-393">Si tratta della stessa libreria utilizzata da Visual Studio per consigliare i nomi delle variabili.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-393">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-394">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-394">Why</span></span>

<span data-ttu-id="2d3ca-395">L'uso di forme plurali di parole per le proprietà della raccolta e i moduli singolari per i tipi e le proprietà di riferimento è idiomatiche in .NET.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-395">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-396">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-396">Mitigations</span></span>

<span data-ttu-id="2d3ca-397">Per disabilitare pluralizer, utilizzare l' `--no-pluralize` opzione on `dotnet ef dbcontext scaffold` o l'opzione `-NoPluralize` on `Scaffold-DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-397">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="2d3ca-398">INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip</span><span class="sxs-lookup"><span data-stu-id="2d3ca-398">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="2d3ca-399">Rilevamento del problema #2568</span><span class="sxs-lookup"><span data-stu-id="2d3ca-399">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="2d3ca-400">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="2d3ca-400">Old behavior</span></span>

<span data-ttu-id="2d3ca-401">EF Core precedente alla 5,0 supportava solo una forma di proprietà di navigazione, rappresentata dall' `INavigation` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-401">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2d3ca-402">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="2d3ca-402">New behavior</span></span>

<span data-ttu-id="2d3ca-403">EF Core 5,0 introduce relazioni molti-a-molti che usano "Ignora spostamento".</span><span class="sxs-lookup"><span data-stu-id="2d3ca-403">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="2d3ca-404">Sono rappresentati dall' `ISkipNavigation` interfaccia e la maggior parte delle funzionalità di `INavigation` è stata spostata in un'interfaccia di base comune: `INavigationBase` .</span><span class="sxs-lookup"><span data-stu-id="2d3ca-404">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="2d3ca-405">Perché</span><span class="sxs-lookup"><span data-stu-id="2d3ca-405">Why</span></span>

<span data-ttu-id="2d3ca-406">La maggior parte delle funzionalità tra le normali e le ignorazioni è la stessa.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-406">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="2d3ca-407">Tuttavia, ignorare le esplorazioni hanno una relazione diversa con le chiavi esterne rispetto alle normali spostamenti, poiché i FKs interessati non si trovano direttamente su entrambe le estremità della relazione, ma piuttosto nell'entità di join.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-407">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2d3ca-408">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="2d3ca-408">Mitigations</span></span>

<span data-ttu-id="2d3ca-409">In molti casi le applicazioni possono passare all'uso della nuova interfaccia di base senza altre modifiche.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-409">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="2d3ca-410">Tuttavia, nei casi in cui la navigazione viene utilizzata per accedere alle proprietà di chiave esterna, il codice dell'applicazione deve essere vincolato solo a spostamenti normali oppure è stato aggiornato per eseguire le operazioni appropriate sia per le operazioni di navigazione normale che per quelle ignorate.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-410">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="2d3ca-411">Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate</span><span class="sxs-lookup"><span data-stu-id="2d3ca-411">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="2d3ca-412">Rilevamento del problema #15873</span><span class="sxs-lookup"><span data-stu-id="2d3ca-412">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="2d3ca-413">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-413">**Old behavior**</span></span>

<span data-ttu-id="2d3ca-414">In precedenza, venivano eseguite query che coinvolgono raccolte correlate seguite da `GroupBy` , nonché alcune query che utilizzano l' `Distinct` autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-414">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="2d3ca-415">Esempio di GroupBy:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-415">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="2d3ca-416">`Distinct` esempio `Distinct` : query specifiche in cui la proiezione della raccolta interna non contiene la chiave primaria:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-416">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="2d3ca-417">Queste query potrebbero restituire risultati non corretti se la raccolta interna contiene duplicati, ma funziona correttamente se tutti gli elementi della raccolta interna sono univoci.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-417">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="2d3ca-418">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-418">**New behavior**</span></span>

<span data-ttu-id="2d3ca-419">Queste query non sono più supportate.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-419">These queries are no longer supported.</span></span> <span data-ttu-id="2d3ca-420">Viene generata un'eccezione che indica che non sono disponibili informazioni sufficienti per compilare correttamente i risultati.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-420">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="2d3ca-421">**Perché**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-421">**Why**</span></span>

<span data-ttu-id="2d3ca-422">Per gli scenari di raccolta correlati è necessario individuare la chiave primaria dell'entità per assegnare entità di raccolta al padre corretto.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-422">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="2d3ca-423">Quando la raccolta interna non usa `GroupBy` o `Distinct` , la chiave primaria mancante può semplicemente essere aggiunta alla proiezione.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-423">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="2d3ca-424">Tuttavia, nel caso di `GroupBy` e non `Distinct` può essere eseguita perché cambierebbe il risultato dell' `GroupBy` `Distinct` operazione o.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-424">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="2d3ca-425">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-425">**Mitigations**</span></span>

<span data-ttu-id="2d3ca-426">Riscrivere la query in modo da non utilizzare le `GroupBy` `Distinct` operazioni o nella raccolta interna ed eseguire tali operazioni sul client.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-426">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="2d3ca-427">L'uso di una raccolta di tipi queryable nella proiezione non è supportato</span><span class="sxs-lookup"><span data-stu-id="2d3ca-427">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="2d3ca-428">Rilevamento del problema #16314</span><span class="sxs-lookup"><span data-stu-id="2d3ca-428">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="2d3ca-429">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-429">**Old behavior**</span></span>

<span data-ttu-id="2d3ca-430">In precedenza era possibile usare la raccolta di un tipo Queryable all'interno della proiezione in alcuni casi, ad esempio come argomento di un `List<T>` costruttore:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-430">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="2d3ca-431">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-431">**New behavior**</span></span>

<span data-ttu-id="2d3ca-432">Queste query non sono più supportate.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-432">These queries are no longer supported.</span></span> <span data-ttu-id="2d3ca-433">Viene generata un'eccezione che indica che non è possibile creare un oggetto di tipo Queryable e suggerire come risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-433">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="2d3ca-434">**Perché**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-434">**Why**</span></span>

<span data-ttu-id="2d3ca-435">Non è possibile materializzare un oggetto di un tipo Queryable, in modo che venga creato automaticamente usando il `List<T>` tipo.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-435">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="2d3ca-436">Questo genererebbe spesso un'eccezione a causa della mancata corrispondenza del tipo che non era molto chiara e potrebbe essere sorprendente per alcuni utenti.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-436">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="2d3ca-437">Si è deciso di riconoscere il modello e generare un'eccezione più significativa.</span><span class="sxs-lookup"><span data-stu-id="2d3ca-437">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="2d3ca-438">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="2d3ca-438">**Mitigations**</span></span>

<span data-ttu-id="2d3ca-439">Aggiungere una `ToList()` chiamata dopo l'oggetto Queryable nella proiezione:</span><span class="sxs-lookup"><span data-stu-id="2d3ca-439">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
