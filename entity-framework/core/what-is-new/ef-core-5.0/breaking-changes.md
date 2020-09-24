---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210367"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="c8569-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="c8569-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="c8569-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="c8569-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="c8569-105">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="c8569-105">Summary</span></span>

| <span data-ttu-id="c8569-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="c8569-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="c8569-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="c8569-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="c8569-108">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="c8569-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="c8569-109">Media</span><span class="sxs-lookup"><span data-stu-id="c8569-109">Medium</span></span>     |
| [<span data-ttu-id="c8569-110">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="c8569-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="c8569-111">Media</span><span class="sxs-lookup"><span data-stu-id="c8569-111">Medium</span></span>     |
| [<span data-ttu-id="c8569-112">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="c8569-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="c8569-113">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-113">Low</span></span>        |
| [<span data-ttu-id="c8569-114">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="c8569-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="c8569-115">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-115">Low</span></span>        |
| [<span data-ttu-id="c8569-116">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="c8569-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="c8569-117">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-117">Low</span></span>        |
| <span data-ttu-id="c8569-118">[Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="c8569-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="c8569-119">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-119">Low</span></span>        |
| [<span data-ttu-id="c8569-120">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="c8569-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="c8569-121">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-121">Low</span></span>        |
| [<span data-ttu-id="c8569-122">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="c8569-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="c8569-123">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-123">Low</span></span>        |
| [<span data-ttu-id="c8569-124">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="c8569-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="c8569-125">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-125">Low</span></span>        |
| [<span data-ttu-id="c8569-126">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="c8569-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="c8569-127">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-127">Low</span></span>        |
| [<span data-ttu-id="c8569-128">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="c8569-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="c8569-129">Bassa</span><span class="sxs-lookup"><span data-stu-id="c8569-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="c8569-130">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="c8569-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="c8569-131">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="c8569-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="c8569-132">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-132">**Old behavior**</span></span>

<span data-ttu-id="c8569-133">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="c8569-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="c8569-134">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="c8569-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="c8569-135">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="c8569-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="c8569-136">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="c8569-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="c8569-137">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-137">**New behavior**</span></span>

<span data-ttu-id="c8569-138">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="c8569-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="c8569-139">Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="c8569-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="c8569-140">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-140">**Why**</span></span>

<span data-ttu-id="c8569-141">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="c8569-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="c8569-142">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-142">**Mitigations**</span></span>

<span data-ttu-id="c8569-143">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="c8569-143">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="c8569-144">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="c8569-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="c8569-145">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="c8569-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="c8569-146">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-146">**Old behavior**</span></span>

<span data-ttu-id="c8569-147">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="c8569-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="c8569-148">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="c8569-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="c8569-149">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-149">**New behavior**</span></span>

<span data-ttu-id="c8569-150">Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="c8569-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="c8569-151">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="c8569-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="c8569-152">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-152">**Why**</span></span>

<span data-ttu-id="c8569-153">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="c8569-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="c8569-154">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-154">**Mitigations**</span></span>

<span data-ttu-id="c8569-155">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="c8569-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="c8569-156">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="c8569-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="c8569-157">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="c8569-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="c8569-158">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-158">**Old behavior**</span></span>

<span data-ttu-id="c8569-159">La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .</span><span class="sxs-lookup"><span data-stu-id="c8569-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="c8569-160">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-160">**New behavior**</span></span>

<span data-ttu-id="c8569-161">La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.</span><span class="sxs-lookup"><span data-stu-id="c8569-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="c8569-162">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-162">**Why**</span></span>

<span data-ttu-id="c8569-163">Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.</span><span class="sxs-lookup"><span data-stu-id="c8569-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="c8569-164">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-164">**Mitigations**</span></span>

<span data-ttu-id="c8569-165">Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="c8569-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="c8569-166">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="c8569-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="c8569-167">Rilevamento del problema #17751</span><span class="sxs-lookup"><span data-stu-id="c8569-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="c8569-168">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-168">**Old behavior**</span></span>

<span data-ttu-id="c8569-169">Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .</span><span class="sxs-lookup"><span data-stu-id="c8569-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="c8569-170">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-170">**New behavior**</span></span>

<span data-ttu-id="c8569-171">La proprietà shadow creata per convenzione è ora denominata `__id` .</span><span class="sxs-lookup"><span data-stu-id="c8569-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="c8569-172">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-172">**Why**</span></span>

<span data-ttu-id="c8569-173">Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c8569-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="c8569-174">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-174">**Mitigations**</span></span>

<span data-ttu-id="c8569-175">Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="c8569-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="c8569-176">Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri</span><span class="sxs-lookup"><span data-stu-id="c8569-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="c8569-177">Rilevamento del problema #17306</span><span class="sxs-lookup"><span data-stu-id="c8569-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="c8569-178">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-178">**Old behavior**</span></span>

<span data-ttu-id="c8569-179">Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.</span><span class="sxs-lookup"><span data-stu-id="c8569-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="c8569-180">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-180">**New behavior**</span></span>

<span data-ttu-id="c8569-181">Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.</span><span class="sxs-lookup"><span data-stu-id="c8569-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="c8569-182">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-182">**Why**</span></span>

<span data-ttu-id="c8569-183">Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.</span><span class="sxs-lookup"><span data-stu-id="c8569-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="c8569-184">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-184">**Mitigations**</span></span>

<span data-ttu-id="c8569-185">I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento.</span><span class="sxs-lookup"><span data-stu-id="c8569-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="c8569-186">Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="c8569-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="c8569-187">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="c8569-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="c8569-188">Rilevamento del problema #17874</span><span class="sxs-lookup"><span data-stu-id="c8569-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="c8569-189">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-189">**Old behavior**</span></span>

<span data-ttu-id="c8569-190">In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="c8569-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="c8569-191">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-191">**New behavior**</span></span>

<span data-ttu-id="c8569-192">L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="c8569-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="c8569-193">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-193">**Why**</span></span>

<span data-ttu-id="c8569-194">Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="c8569-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="c8569-195">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-195">**Mitigations**</span></span>

<span data-ttu-id="c8569-196">Usare la nuova API.</span><span class="sxs-lookup"><span data-stu-id="c8569-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="c8569-197">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="c8569-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="c8569-198">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="c8569-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="c8569-199">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-199">**Old behavior**</span></span>

<span data-ttu-id="c8569-200">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="c8569-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="c8569-201">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-201">**New behavior**</span></span>

<span data-ttu-id="c8569-202">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c8569-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="c8569-203">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-203">**Why**</span></span>

<span data-ttu-id="c8569-204">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="c8569-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="c8569-205">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-205">**Mitigations**</span></span>

<span data-ttu-id="c8569-206">Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="c8569-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="c8569-207">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="c8569-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="c8569-208">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="c8569-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="c8569-209">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-209">**Old behavior**</span></span>

<span data-ttu-id="c8569-210">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="c8569-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="c8569-211">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-211">**New behavior**</span></span>

<span data-ttu-id="c8569-212">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="c8569-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="c8569-213">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="c8569-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="c8569-214">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-214">**Why**</span></span>

<span data-ttu-id="c8569-215">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="c8569-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="c8569-216">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="c8569-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="c8569-217">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-217">**Mitigations**</span></span>

<span data-ttu-id="c8569-218">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="c8569-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
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

<span data-ttu-id="c8569-219">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="c8569-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="c8569-220">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="c8569-220">Discriminators are read-only</span></span>

[<span data-ttu-id="c8569-221">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="c8569-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="c8569-222">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-222">**Old behavior**</span></span>

<span data-ttu-id="c8569-223">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="c8569-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="c8569-224">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-224">**New behavior**</span></span>

<span data-ttu-id="c8569-225">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c8569-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="c8569-226">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-226">**Why**</span></span>

<span data-ttu-id="c8569-227">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="c8569-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="c8569-228">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-228">**Mitigations**</span></span>

<span data-ttu-id="c8569-229">Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="c8569-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="c8569-230">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="c8569-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="c8569-231">Rilevamento del problema #18903</span><span class="sxs-lookup"><span data-stu-id="c8569-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="c8569-232">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-232">**Old behavior**</span></span>

<span data-ttu-id="c8569-233">È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core.</span><span class="sxs-lookup"><span data-stu-id="c8569-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="c8569-234">Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.</span><span class="sxs-lookup"><span data-stu-id="c8569-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="c8569-235">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-235">**New behavior**</span></span>

<span data-ttu-id="c8569-236">Le API per la definizione della query sono deprecate.</span><span class="sxs-lookup"><span data-stu-id="c8569-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="c8569-237">Sono state introdotte nuove API specifiche del provider.</span><span class="sxs-lookup"><span data-stu-id="c8569-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="c8569-238">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-238">**Why**</span></span>

<span data-ttu-id="c8569-239">Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="c8569-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="c8569-240">Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.</span><span class="sxs-lookup"><span data-stu-id="c8569-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="c8569-241">Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.</span><span class="sxs-lookup"><span data-stu-id="c8569-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="c8569-242">Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider in memoria per entità senza chiave (analogamente alle viste di database nei database relazionali).</span><span class="sxs-lookup"><span data-stu-id="c8569-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="c8569-243">Questa definizione consente di testare facilmente l'applicazione in base al database in memoria.</span><span class="sxs-lookup"><span data-stu-id="c8569-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="c8569-244">In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento.</span><span class="sxs-lookup"><span data-stu-id="c8569-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="c8569-245">Quindi abbiamo deciso di semplificare il concetto.</span><span class="sxs-lookup"><span data-stu-id="c8569-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="c8569-246">È stata creata una query di definizione basata su LINQ esclusiva per il provider in memoria e gestita in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="c8569-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="c8569-247">Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="c8569-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="c8569-248">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-248">**Mitigations**</span></span>

<span data-ttu-id="c8569-249">Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c8569-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="c8569-250">Per il provider in memoria, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c8569-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="c8569-251">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="c8569-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="c8569-252">Rilevamento del problema #20294</span><span class="sxs-lookup"><span data-stu-id="c8569-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="c8569-253">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="c8569-253">**Old behavior**</span></span>

<span data-ttu-id="c8569-254">EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="c8569-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="c8569-255">Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="c8569-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="c8569-256">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="c8569-256">**New behavior**</span></span>

<span data-ttu-id="c8569-257">I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.</span><span class="sxs-lookup"><span data-stu-id="c8569-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="c8569-258">**Perché**</span><span class="sxs-lookup"><span data-stu-id="c8569-258">**Why**</span></span>

<span data-ttu-id="c8569-259">I metodi specifici del provider vengono mappati a una funzione di database.</span><span class="sxs-lookup"><span data-stu-id="c8569-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="c8569-260">Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ.</span><span class="sxs-lookup"><span data-stu-id="c8569-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="c8569-261">È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client.</span><span class="sxs-lookup"><span data-stu-id="c8569-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="c8569-262">Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client.</span><span class="sxs-lookup"><span data-stu-id="c8569-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="c8569-263">Poiché il provider InMemory è un *database*diverso, questi metodi non sono disponibili per questo provider.</span><span class="sxs-lookup"><span data-stu-id="c8569-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="c8569-264">Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c8569-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="c8569-265">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="c8569-265">**Mitigations**</span></span>

<span data-ttu-id="c8569-266">Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.</span><span class="sxs-lookup"><span data-stu-id="c8569-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
