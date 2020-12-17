---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 7a13c9a6f6bd299991c379ec490480e1fbb4ba46
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635471"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="46a65-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="46a65-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="46a65-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="46a65-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="46a65-105">Summary</span><span class="sxs-lookup"><span data-stu-id="46a65-105">Summary</span></span>

| <span data-ttu-id="46a65-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="46a65-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="46a65-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="46a65-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="46a65-108">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="46a65-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="46a65-109">Media</span><span class="sxs-lookup"><span data-stu-id="46a65-109">Medium</span></span>     |
| [<span data-ttu-id="46a65-110">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="46a65-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="46a65-111">Media</span><span class="sxs-lookup"><span data-stu-id="46a65-111">Medium</span></span>     |
| [<span data-ttu-id="46a65-112">Le esplorazioni di riferimento non null non vengono sovrascritte dalle query</span><span class="sxs-lookup"><span data-stu-id="46a65-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="46a65-113">Media</span><span class="sxs-lookup"><span data-stu-id="46a65-113">Medium</span></span>     |
| [<span data-ttu-id="46a65-114">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="46a65-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="46a65-115">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-115">Low</span></span>        |
| [<span data-ttu-id="46a65-116">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="46a65-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="46a65-117">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-117">Low</span></span>        |
| [<span data-ttu-id="46a65-118">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="46a65-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="46a65-119">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-119">Low</span></span>        |
| <span data-ttu-id="46a65-120">[Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="46a65-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="46a65-121">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-121">Low</span></span>        |
| [<span data-ttu-id="46a65-122">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="46a65-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="46a65-123">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-123">Low</span></span>        |
| [<span data-ttu-id="46a65-124">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="46a65-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="46a65-125">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-125">Low</span></span>        |
| [<span data-ttu-id="46a65-126">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="46a65-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="46a65-127">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-127">Low</span></span>        |
| [<span data-ttu-id="46a65-128">Toview () viene considerato in modo diverso dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="46a65-128">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="46a65-129">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-129">Low</span></span>        |
| [<span data-ttu-id="46a65-130">ToTable (null) contrassegna il tipo di entità come non mappato a una tabella</span><span class="sxs-lookup"><span data-stu-id="46a65-130">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="46a65-131">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-131">Low</span></span>        |
| [<span data-ttu-id="46a65-132">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="46a65-132">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="46a65-133">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-133">Low</span></span>        |
| [<span data-ttu-id="46a65-134">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="46a65-134">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="46a65-135">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-135">Low</span></span>        |
| [<span data-ttu-id="46a65-136">IProperty. getColumnName () è obsoleto</span><span class="sxs-lookup"><span data-stu-id="46a65-136">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="46a65-137">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-137">Low</span></span>        |
| [<span data-ttu-id="46a65-138">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="46a65-138">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="46a65-139">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-139">Low</span></span>        |
| [<span data-ttu-id="46a65-140">Un pluralizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="46a65-140">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="46a65-141">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-141">Low</span></span>        |
| [<span data-ttu-id="46a65-142">INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip</span><span class="sxs-lookup"><span data-stu-id="46a65-142">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="46a65-143">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-143">Low</span></span>        |
| [<span data-ttu-id="46a65-144">Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate</span><span class="sxs-lookup"><span data-stu-id="46a65-144">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="46a65-145">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-145">Low</span></span>        |
| [<span data-ttu-id="46a65-146">L'uso di una raccolta di tipi queryable nella proiezione non è supportato</span><span class="sxs-lookup"><span data-stu-id="46a65-146">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="46a65-147">Bassa</span><span class="sxs-lookup"><span data-stu-id="46a65-147">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="46a65-148">Modifiche a media Impact</span><span class="sxs-lookup"><span data-stu-id="46a65-148">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="46a65-149">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="46a65-149">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="46a65-150">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="46a65-150">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-151">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-151">Old behavior</span></span>

<span data-ttu-id="46a65-152">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="46a65-152">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="46a65-153">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="46a65-153">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-154">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-154">New behavior</span></span>

<span data-ttu-id="46a65-155">Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="46a65-155">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="46a65-156">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="46a65-156">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="46a65-157">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-157">Why</span></span>

<span data-ttu-id="46a65-158">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="46a65-158">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-159">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-159">Mitigations</span></span>

<span data-ttu-id="46a65-160">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="46a65-160">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="46a65-161">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="46a65-161">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="46a65-162">Rilevamento del problema #18903</span><span class="sxs-lookup"><span data-stu-id="46a65-162">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-163">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-163">Old behavior</span></span>

<span data-ttu-id="46a65-164">È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core.</span><span class="sxs-lookup"><span data-stu-id="46a65-164">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="46a65-165">Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.</span><span class="sxs-lookup"><span data-stu-id="46a65-165">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-166">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-166">New behavior</span></span>

<span data-ttu-id="46a65-167">Le API per la definizione della query sono deprecate.</span><span class="sxs-lookup"><span data-stu-id="46a65-167">APIs for defining query are deprecated.</span></span> <span data-ttu-id="46a65-168">Sono state introdotte nuove API specifiche del provider.</span><span class="sxs-lookup"><span data-stu-id="46a65-168">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-169">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-169">Why</span></span>

<span data-ttu-id="46a65-170">Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="46a65-170">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="46a65-171">Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.</span><span class="sxs-lookup"><span data-stu-id="46a65-171">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="46a65-172">Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.</span><span class="sxs-lookup"><span data-stu-id="46a65-172">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="46a65-173">Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider In-Memory per le entità senza chiave (analogamente alle viste di database nei database relazionali).</span><span class="sxs-lookup"><span data-stu-id="46a65-173">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="46a65-174">Questa definizione consente di testare facilmente l'applicazione in base al database in memoria.</span><span class="sxs-lookup"><span data-stu-id="46a65-174">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="46a65-175">In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento.</span><span class="sxs-lookup"><span data-stu-id="46a65-175">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="46a65-176">Quindi abbiamo deciso di semplificare il concetto.</span><span class="sxs-lookup"><span data-stu-id="46a65-176">So we decided to simplify the concept.</span></span> <span data-ttu-id="46a65-177">È stata creata una query di definizione basata su LINQ esclusiva per In-Memory provider e gestirli in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="46a65-177">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="46a65-178">Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="46a65-178">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-179">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-179">Mitigations</span></span>

<span data-ttu-id="46a65-180">Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="46a65-180">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="46a65-181">Per il provider di In-Memory, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="46a65-181">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="46a65-182">Le esplorazioni di riferimento non null non vengono sovrascritte dalle query</span><span class="sxs-lookup"><span data-stu-id="46a65-182">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="46a65-183">Rilevamento del problema #2693</span><span class="sxs-lookup"><span data-stu-id="46a65-183">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-184">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-184">Old behavior</span></span>

<span data-ttu-id="46a65-185">In EF Core 3,1, le esplorazioni dei riferimenti inizializzate in modo eager con valori non null verrebbero talvolta sovrascritte dalle istanze di entità dal database, indipendentemente dal fatto che i valori delle chiavi corrispondano o meno.</span><span class="sxs-lookup"><span data-stu-id="46a65-185">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="46a65-186">Tuttavia, in altri casi, EF Core 3,1 esegue l'operazione opposta e lascia il valore non null esistente.</span><span class="sxs-lookup"><span data-stu-id="46a65-186">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-187">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-187">New behavior</span></span>

<span data-ttu-id="46a65-188">A partire da EF Core 5,0, le navigazioni di riferimento non null non vengono mai sovrascritte dalle istanze restituite da una query.</span><span class="sxs-lookup"><span data-stu-id="46a65-188">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="46a65-189">Si noti che l'inizializzazione eager di una _raccolta_ di spostamento in una raccolta vuota è ancora supportata.</span><span class="sxs-lookup"><span data-stu-id="46a65-189">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-190">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-190">Why</span></span>

<span data-ttu-id="46a65-191">L'inizializzazione di una proprietà di navigazione di riferimento su un'istanza di entità "vuota" restituisce uno stato ambiguo.</span><span class="sxs-lookup"><span data-stu-id="46a65-191">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="46a65-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46a65-192">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="46a65-193">In genere, una query per i Blog e gli autori creerà prima di tutto le `Blog` istanze e quindi imposterà le `Author` istanze appropriate in base ai dati restituiti dal database.</span><span class="sxs-lookup"><span data-stu-id="46a65-193">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="46a65-194">Tuttavia, in questo caso ogni `Blog.Author` proprietà è già inizializzata su un oggetto vuoto `Author` .</span><span class="sxs-lookup"><span data-stu-id="46a65-194">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="46a65-195">Ad eccezione di EF Core non è possibile verificare che l'istanza sia "vuota".</span><span class="sxs-lookup"><span data-stu-id="46a65-195">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="46a65-196">Quindi, sovrascrivendo questa istanza potrebbe essere possibile eliminare un valore valido in modo invisibile all'utente `Author` .</span><span class="sxs-lookup"><span data-stu-id="46a65-196">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="46a65-197">Pertanto, EF Core 5,0 ora non sovrascrive in modo coerente una navigazione già inizializzata.</span><span class="sxs-lookup"><span data-stu-id="46a65-197">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="46a65-198">Questo nuovo comportamento è anche allineato con il comportamento di EF6 nella maggior parte dei casi, sebbene durante l'analisi sono stati rilevati anche alcuni casi di incoerenza in EF6.</span><span class="sxs-lookup"><span data-stu-id="46a65-198">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="46a65-199">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-199">Mitigations</span></span>

<span data-ttu-id="46a65-200">Se viene rilevata questa interruzione, la correzione consiste nell'arrestare l'inizializzazione delle proprietà di navigazione dei riferimenti.</span><span class="sxs-lookup"><span data-stu-id="46a65-200">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="46a65-201">Modifiche a basso effetto</span><span class="sxs-lookup"><span data-stu-id="46a65-201">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="46a65-202">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="46a65-202">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="46a65-203">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="46a65-203">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-204">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-204">Old behavior</span></span>

<span data-ttu-id="46a65-205">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="46a65-205">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="46a65-206">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="46a65-206">However, it only ever affected database creation.</span></span> <span data-ttu-id="46a65-207">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="46a65-207">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="46a65-208">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="46a65-208">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-209">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-209">New behavior</span></span>

<span data-ttu-id="46a65-210">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="46a65-210">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="46a65-211">Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="46a65-211">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-212">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-212">Why</span></span>

<span data-ttu-id="46a65-213">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="46a65-213">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-214">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-214">Mitigations</span></span>

<span data-ttu-id="46a65-215">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="46a65-215">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="46a65-216">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="46a65-216">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="46a65-217">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="46a65-217">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-218">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-218">Old behavior</span></span>

<span data-ttu-id="46a65-219">La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .</span><span class="sxs-lookup"><span data-stu-id="46a65-219">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-220">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-220">New behavior</span></span>

<span data-ttu-id="46a65-221">La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.</span><span class="sxs-lookup"><span data-stu-id="46a65-221">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-222">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-222">Why</span></span>

<span data-ttu-id="46a65-223">Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.</span><span class="sxs-lookup"><span data-stu-id="46a65-223">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-224">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-224">Mitigations</span></span>

<span data-ttu-id="46a65-225">Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="46a65-225">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="46a65-226">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="46a65-226">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="46a65-227">Rilevamento del problema #17751</span><span class="sxs-lookup"><span data-stu-id="46a65-227">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-228">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-228">Old behavior</span></span>

<span data-ttu-id="46a65-229">Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .</span><span class="sxs-lookup"><span data-stu-id="46a65-229">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-230">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-230">New behavior</span></span>

<span data-ttu-id="46a65-231">La proprietà shadow creata per convenzione è ora denominata `__id` .</span><span class="sxs-lookup"><span data-stu-id="46a65-231">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-232">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-232">Why</span></span>

<span data-ttu-id="46a65-233">Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="46a65-233">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-234">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-234">Mitigations</span></span>

<span data-ttu-id="46a65-235">Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="46a65-235">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="46a65-236">Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri</span><span class="sxs-lookup"><span data-stu-id="46a65-236">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="46a65-237">Rilevamento del problema #17306</span><span class="sxs-lookup"><span data-stu-id="46a65-237">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-238">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-238">Old behavior</span></span>

<span data-ttu-id="46a65-239">Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.</span><span class="sxs-lookup"><span data-stu-id="46a65-239">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-240">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-240">New behavior</span></span>

<span data-ttu-id="46a65-241">Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.</span><span class="sxs-lookup"><span data-stu-id="46a65-241">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-242">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-242">Why</span></span>

<span data-ttu-id="46a65-243">Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.</span><span class="sxs-lookup"><span data-stu-id="46a65-243">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-244">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-244">Mitigations</span></span>

<span data-ttu-id="46a65-245">I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento.</span><span class="sxs-lookup"><span data-stu-id="46a65-245">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="46a65-246">Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/dotnet/efcore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="46a65-246">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="46a65-247">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="46a65-247">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="46a65-248">Rilevamento del problema #17874</span><span class="sxs-lookup"><span data-stu-id="46a65-248">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-249">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-249">Old behavior</span></span>

<span data-ttu-id="46a65-250">In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="46a65-250">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-251">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-251">New behavior</span></span>

<span data-ttu-id="46a65-252">L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="46a65-252">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-253">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-253">Why</span></span>

<span data-ttu-id="46a65-254">Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="46a65-254">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-255">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-255">Mitigations</span></span>

<span data-ttu-id="46a65-256">Usare la nuova API.</span><span class="sxs-lookup"><span data-stu-id="46a65-256">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="46a65-257">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="46a65-257">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="46a65-258">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="46a65-258">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-259">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-259">Old behavior</span></span>

<span data-ttu-id="46a65-260">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="46a65-260">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-261">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-261">New behavior</span></span>

<span data-ttu-id="46a65-262">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="46a65-262">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-263">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-263">Why</span></span>

<span data-ttu-id="46a65-264">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="46a65-264">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-265">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-265">Mitigations</span></span>

<span data-ttu-id="46a65-266">Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="46a65-266">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="46a65-267">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="46a65-267">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="46a65-268">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="46a65-268">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-269">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-269">Old behavior</span></span>

<span data-ttu-id="46a65-270">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="46a65-270">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-271">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-271">New behavior</span></span>

<span data-ttu-id="46a65-272">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="46a65-272">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="46a65-273">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="46a65-273">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-274">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-274">Why</span></span>

<span data-ttu-id="46a65-275">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="46a65-275">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="46a65-276">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="46a65-276">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-277">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-277">Mitigations</span></span>

<span data-ttu-id="46a65-278">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="46a65-278">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="46a65-279">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="46a65-279">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="46a65-280">Toview () viene considerato in modo diverso dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="46a65-280">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="46a65-281">Rilevamento del problema #2725</span><span class="sxs-lookup"><span data-stu-id="46a65-281">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-282">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-282">Old behavior</span></span>

<span data-ttu-id="46a65-283">La chiamata a `ToView(string)` ha fatto in modo che le migrazioni ignorino il tipo di entità oltre a eseguire il mapping a una vista.</span><span class="sxs-lookup"><span data-stu-id="46a65-283">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-284">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-284">New behavior</span></span>

<span data-ttu-id="46a65-285">Ora `ToView(string)` contrassegna il tipo di entità come non mappato a una tabella, oltre a eseguirne il mapping a una vista.</span><span class="sxs-lookup"><span data-stu-id="46a65-285">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="46a65-286">Ciò comporta la prima migrazione dopo l'aggiornamento a EF Core 5 per tentare di eliminare la tabella predefinita per questo tipo di entità perché non viene più ignorata.</span><span class="sxs-lookup"><span data-stu-id="46a65-286">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-287">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-287">Why</span></span>

<span data-ttu-id="46a65-288">EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, pertanto `ToView` non è più un indicatore valido che deve essere ignorato dalle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="46a65-288">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-289">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-289">Mitigations</span></span>

<span data-ttu-id="46a65-290">Usare il codice seguente per contrassegnare la tabella mappata come esclusa dalle migrazioni:</span><span class="sxs-lookup"><span data-stu-id="46a65-290">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="46a65-291">ToTable (null) contrassegna il tipo di entità come non mappato a una tabella</span><span class="sxs-lookup"><span data-stu-id="46a65-291">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="46a65-292">Rilevamento del problema #21172</span><span class="sxs-lookup"><span data-stu-id="46a65-292">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-293">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-293">Old behavior</span></span>

<span data-ttu-id="46a65-294">`ToTable(null)` Reimposta il nome della tabella sul valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="46a65-294">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-295">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-295">New behavior</span></span>

<span data-ttu-id="46a65-296">`ToTable(null)` ora contrassegna il tipo di entità come non mappato ad alcuna tabella.</span><span class="sxs-lookup"><span data-stu-id="46a65-296">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-297">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-297">Why</span></span>

<span data-ttu-id="46a65-298">EF Core consente ora di eseguire il mapping simultaneo di un tipo di entità a una tabella e a una vista, quindi `ToTable(null)` viene usato per indicare che non è mappato ad alcuna tabella.</span><span class="sxs-lookup"><span data-stu-id="46a65-298">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-299">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-299">Mitigations</span></span>

<span data-ttu-id="46a65-300">Usare il codice seguente per reimpostare il nome della tabella sul valore predefinito se non è mappato a una vista o a un DbFunction:</span><span class="sxs-lookup"><span data-stu-id="46a65-300">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="46a65-301">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="46a65-301">Discriminators are read-only</span></span>

[<span data-ttu-id="46a65-302">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="46a65-302">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-303">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-303">Old behavior</span></span>

<span data-ttu-id="46a65-304">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="46a65-304">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-305">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-305">New behavior</span></span>

<span data-ttu-id="46a65-306">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="46a65-306">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-307">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-307">Why</span></span>

<span data-ttu-id="46a65-308">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="46a65-308">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-309">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-309">Mitigations</span></span>

<span data-ttu-id="46a65-310">Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="46a65-310">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="46a65-311">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="46a65-311">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="46a65-312">Rilevamento del problema #20294</span><span class="sxs-lookup"><span data-stu-id="46a65-312">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-313">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-313">Old behavior</span></span>

<span data-ttu-id="46a65-314">EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="46a65-314">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="46a65-315">Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="46a65-315">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-316">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-316">New behavior</span></span>

<span data-ttu-id="46a65-317">I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.</span><span class="sxs-lookup"><span data-stu-id="46a65-317">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-318">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-318">Why</span></span>

<span data-ttu-id="46a65-319">I metodi specifici del provider vengono mappati a una funzione di database.</span><span class="sxs-lookup"><span data-stu-id="46a65-319">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="46a65-320">Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ.</span><span class="sxs-lookup"><span data-stu-id="46a65-320">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="46a65-321">È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client.</span><span class="sxs-lookup"><span data-stu-id="46a65-321">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="46a65-322">Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client.</span><span class="sxs-lookup"><span data-stu-id="46a65-322">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="46a65-323">Poiché il provider InMemory è un *database* diverso, questi metodi non sono disponibili per questo provider.</span><span class="sxs-lookup"><span data-stu-id="46a65-323">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="46a65-324">Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="46a65-324">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-325">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-325">Mitigations</span></span>

<span data-ttu-id="46a65-326">Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.</span><span class="sxs-lookup"><span data-stu-id="46a65-326">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="46a65-327">IProperty. getColumnName () è obsoleto</span><span class="sxs-lookup"><span data-stu-id="46a65-327">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="46a65-328">Rilevamento del problema #2266</span><span class="sxs-lookup"><span data-stu-id="46a65-328">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-329">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-329">Old behavior</span></span>

<span data-ttu-id="46a65-330">`GetColumnName()` Restituisce il nome della colonna a cui è stato eseguito il mapping di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="46a65-330">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-331">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-331">New behavior</span></span>

<span data-ttu-id="46a65-332">`GetColumnName()` restituisce comunque il nome di una colonna a cui è stato eseguito il mapping di una proprietà, ma questo comportamento è ambiguo perché EF Core 5 supporta TPT e il mapping simultaneo a una vista o a una funzione in cui questi mapping potrebbero utilizzare nomi di colonna diversi per la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="46a65-332">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-333">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-333">Why</span></span>

<span data-ttu-id="46a65-334">Questo metodo è stato contrassegnato come obsoleto per guidare gli utenti a un sovraccarico più accurato <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .</span><span class="sxs-lookup"><span data-stu-id="46a65-334">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-335">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-335">Mitigations</span></span>

<span data-ttu-id="46a65-336">Usare il codice seguente per ottenere il nome della colonna per una tabella specifica:</span><span class="sxs-lookup"><span data-stu-id="46a65-336">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="46a65-337">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="46a65-337">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="46a65-338">Rilevamento del problema #21089</span><span class="sxs-lookup"><span data-stu-id="46a65-338">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-339">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-339">Old behavior</span></span>

<span data-ttu-id="46a65-340">In precedenza era possibile definire un solo indice su un determinato set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="46a65-340">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="46a65-341">Il nome del database di un indice è stato configurato con IndexBuilder. HasName.</span><span class="sxs-lookup"><span data-stu-id="46a65-341">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-342">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-342">New behavior</span></span>

<span data-ttu-id="46a65-343">Sono ora consentiti più indici nello stesso set o proprietà.</span><span class="sxs-lookup"><span data-stu-id="46a65-343">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="46a65-344">Questi indici sono ora distinti da un nome nel modello.</span><span class="sxs-lookup"><span data-stu-id="46a65-344">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="46a65-345">Per convenzione, il nome del modello viene utilizzato come nome del database. Tuttavia, può anche essere configurato in modo indipendente utilizzando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="46a65-345">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-346">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-346">Why</span></span>

<span data-ttu-id="46a65-347">In futuro, vorremmo consentire l'abilitazione di indici sia ascendenti che decrescenti con regole di confronto diverse nello stesso set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="46a65-347">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="46a65-348">Questa modifica consente di spostarsi in un altro passaggio della direzione.</span><span class="sxs-lookup"><span data-stu-id="46a65-348">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-349">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-349">Mitigations</span></span>

<span data-ttu-id="46a65-350">Il codice precedentemente chiamato IndexBuilder. HasName deve essere aggiornato in modo da chiamare HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="46a65-350">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="46a65-351">Se il progetto include migrazioni generate prima della versione 2.0.0 di EF Core, è possibile ignorare l'avviso in tali file ed eliminarlo aggiungendo `#pragma warning disable 612, 618` .</span><span class="sxs-lookup"><span data-stu-id="46a65-351">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="46a65-352">Un pluralizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="46a65-352">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="46a65-353">Rilevamento del problema #11160</span><span class="sxs-lookup"><span data-stu-id="46a65-353">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-354">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-354">Old behavior</span></span>

<span data-ttu-id="46a65-355">In precedenza era necessario installare un pacchetto pluralizer separato per plurali DbSet e i nomi di navigazione della raccolta e i nomi di tabella singolari durante l'impalcatura di un DbContext e i tipi di entità per reverse engineering uno schema di database.</span><span class="sxs-lookup"><span data-stu-id="46a65-355">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-356">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-356">New behavior</span></span>

<span data-ttu-id="46a65-357">EF Core ora include un pluralizer che usa la libreria [umanizzator](https://github.com/Humanizr/Humanizer) .</span><span class="sxs-lookup"><span data-stu-id="46a65-357">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="46a65-358">Si tratta della stessa libreria utilizzata da Visual Studio per consigliare i nomi delle variabili.</span><span class="sxs-lookup"><span data-stu-id="46a65-358">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-359">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-359">Why</span></span>

<span data-ttu-id="46a65-360">L'uso di forme plurali di parole per le proprietà della raccolta e i moduli singolari per i tipi e le proprietà di riferimento è idiomatiche in .NET.</span><span class="sxs-lookup"><span data-stu-id="46a65-360">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-361">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-361">Mitigations</span></span>

<span data-ttu-id="46a65-362">Per disabilitare pluralizer, utilizzare l' `--no-pluralize` opzione on `dotnet ef dbcontext scaffold` o l'opzione `-NoPluralize` on `Scaffold-DbContext` .</span><span class="sxs-lookup"><span data-stu-id="46a65-362">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="46a65-363">INavigationBase sostituisce INavigation in alcune API per supportare le navigazioni Skip</span><span class="sxs-lookup"><span data-stu-id="46a65-363">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="46a65-364">Rilevamento del problema #2568</span><span class="sxs-lookup"><span data-stu-id="46a65-364">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="46a65-365">Comportamento precedente</span><span class="sxs-lookup"><span data-stu-id="46a65-365">Old behavior</span></span>

<span data-ttu-id="46a65-366">EF Core precedente alla 5,0 supportava solo una forma di proprietà di navigazione, rappresentata dall' `INavigation` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="46a65-366">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="46a65-367">Nuovo comportamento</span><span class="sxs-lookup"><span data-stu-id="46a65-367">New behavior</span></span>

<span data-ttu-id="46a65-368">EF Core 5,0 introduce relazioni molti-a-molti che usano "Ignora spostamento".</span><span class="sxs-lookup"><span data-stu-id="46a65-368">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="46a65-369">Sono rappresentati dall' `ISkipNavigation` interfaccia e la maggior parte delle funzionalità di `INavigation` è stata spostata in un'interfaccia di base comune: `INavigationBase` .</span><span class="sxs-lookup"><span data-stu-id="46a65-369">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="46a65-370">Perché</span><span class="sxs-lookup"><span data-stu-id="46a65-370">Why</span></span>

<span data-ttu-id="46a65-371">La maggior parte delle funzionalità tra le normali e le ignorazioni è la stessa.</span><span class="sxs-lookup"><span data-stu-id="46a65-371">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="46a65-372">Tuttavia, ignorare le esplorazioni hanno una relazione diversa con le chiavi esterne rispetto alle normali spostamenti, poiché i FKs interessati non si trovano direttamente su entrambe le estremità della relazione, ma piuttosto nell'entità di join.</span><span class="sxs-lookup"><span data-stu-id="46a65-372">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="46a65-373">Soluzioni di prevenzione</span><span class="sxs-lookup"><span data-stu-id="46a65-373">Mitigations</span></span>

<span data-ttu-id="46a65-374">In molti casi le applicazioni possono passare all'uso della nuova interfaccia di base senza altre modifiche.</span><span class="sxs-lookup"><span data-stu-id="46a65-374">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="46a65-375">Tuttavia, nei casi in cui la navigazione viene utilizzata per accedere alle proprietà di chiave esterna, il codice dell'applicazione deve essere vincolato solo a spostamenti normali oppure è stato aggiornato per eseguire le operazioni appropriate sia per le operazioni di navigazione normale che per quelle ignorate.</span><span class="sxs-lookup"><span data-stu-id="46a65-375">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="46a65-376">Alcune query con raccolta correlata che usano `Distinct` o `GroupBy` non sono più supportate</span><span class="sxs-lookup"><span data-stu-id="46a65-376">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="46a65-377">Rilevamento del problema #15873</span><span class="sxs-lookup"><span data-stu-id="46a65-377">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="46a65-378">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="46a65-378">**Old behavior**</span></span>

<span data-ttu-id="46a65-379">In precedenza, venivano eseguite query che coinvolgono raccolte correlate seguite da `GroupBy` , nonché alcune query che utilizzano l' `Distinct` autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="46a65-379">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="46a65-380">Esempio di GroupBy:</span><span class="sxs-lookup"><span data-stu-id="46a65-380">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="46a65-381">`Distinct` esempio `Distinct` : query specifiche in cui la proiezione della raccolta interna non contiene la chiave primaria:</span><span class="sxs-lookup"><span data-stu-id="46a65-381">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="46a65-382">Queste query potrebbero restituire risultati non corretti se la raccolta interna contiene duplicati, ma funziona correttamente se tutti gli elementi della raccolta interna sono univoci.</span><span class="sxs-lookup"><span data-stu-id="46a65-382">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="46a65-383">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="46a65-383">**New behavior**</span></span>

<span data-ttu-id="46a65-384">Queste query non sono più supportate.</span><span class="sxs-lookup"><span data-stu-id="46a65-384">These queries are no longer supported.</span></span> <span data-ttu-id="46a65-385">Viene generata un'eccezione che indica che non sono disponibili informazioni sufficienti per compilare correttamente i risultati.</span><span class="sxs-lookup"><span data-stu-id="46a65-385">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="46a65-386">**Perché**</span><span class="sxs-lookup"><span data-stu-id="46a65-386">**Why**</span></span>

<span data-ttu-id="46a65-387">Per gli scenari di raccolta correlati è necessario individuare la chiave primaria dell'entità per assegnare entità di raccolta al padre corretto.</span><span class="sxs-lookup"><span data-stu-id="46a65-387">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="46a65-388">Quando la raccolta interna non usa `GroupBy` o `Distinct` , la chiave primaria mancante può semplicemente essere aggiunta alla proiezione.</span><span class="sxs-lookup"><span data-stu-id="46a65-388">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="46a65-389">Tuttavia, nel caso di `GroupBy` e non `Distinct` può essere eseguita perché cambierebbe il risultato dell' `GroupBy` `Distinct` operazione o.</span><span class="sxs-lookup"><span data-stu-id="46a65-389">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="46a65-390">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="46a65-390">**Mitigations**</span></span>

<span data-ttu-id="46a65-391">Riscrivere la query in modo da non utilizzare le `GroupBy` `Distinct` operazioni o nella raccolta interna ed eseguire tali operazioni sul client.</span><span class="sxs-lookup"><span data-stu-id="46a65-391">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="46a65-392">L'uso di una raccolta di tipi queryable nella proiezione non è supportato</span><span class="sxs-lookup"><span data-stu-id="46a65-392">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="46a65-393">Rilevamento del problema #16314</span><span class="sxs-lookup"><span data-stu-id="46a65-393">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="46a65-394">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="46a65-394">**Old behavior**</span></span>

<span data-ttu-id="46a65-395">In precedenza era possibile usare la raccolta di un tipo Queryable all'interno della proiezione in alcuni casi, ad esempio come argomento di un `List<T>` costruttore:</span><span class="sxs-lookup"><span data-stu-id="46a65-395">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="46a65-396">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="46a65-396">**New behavior**</span></span>

<span data-ttu-id="46a65-397">Queste query non sono più supportate.</span><span class="sxs-lookup"><span data-stu-id="46a65-397">These queries are no longer supported.</span></span> <span data-ttu-id="46a65-398">Viene generata un'eccezione che indica che non è possibile creare un oggetto di tipo Queryable e suggerire come risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="46a65-398">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="46a65-399">**Perché**</span><span class="sxs-lookup"><span data-stu-id="46a65-399">**Why**</span></span>

<span data-ttu-id="46a65-400">Non è possibile materializzare un oggetto di un tipo Queryable, in modo che venga creato automaticamente usando il `List<T>` tipo.</span><span class="sxs-lookup"><span data-stu-id="46a65-400">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="46a65-401">Questo genererebbe spesso un'eccezione a causa della mancata corrispondenza del tipo che non era molto chiara e potrebbe essere sorprendente per alcuni utenti.</span><span class="sxs-lookup"><span data-stu-id="46a65-401">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="46a65-402">Si è deciso di riconoscere il modello e generare un'eccezione più significativa.</span><span class="sxs-lookup"><span data-stu-id="46a65-402">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="46a65-403">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="46a65-403">**Mitigations**</span></span>

<span data-ttu-id="46a65-404">Aggiungere una `ToList()` chiamata dopo l'oggetto Queryable nella proiezione:</span><span class="sxs-lookup"><span data-stu-id="46a65-404">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
