---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065641"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="a1af0-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="a1af0-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="a1af0-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="a1af0-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="a1af0-105">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="a1af0-105">Summary</span></span>

| <span data-ttu-id="a1af0-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="a1af0-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="a1af0-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="a1af0-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="a1af0-108">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="a1af0-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="a1af0-109">Media</span><span class="sxs-lookup"><span data-stu-id="a1af0-109">Medium</span></span>     |
| [<span data-ttu-id="a1af0-110">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="a1af0-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="a1af0-111">Media</span><span class="sxs-lookup"><span data-stu-id="a1af0-111">Medium</span></span>     |
| [<span data-ttu-id="a1af0-112">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="a1af0-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="a1af0-113">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-113">Low</span></span>        |
| [<span data-ttu-id="a1af0-114">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="a1af0-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="a1af0-115">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-115">Low</span></span>        |
| [<span data-ttu-id="a1af0-116">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="a1af0-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="a1af0-117">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-117">Low</span></span>        |
| <span data-ttu-id="a1af0-118">[Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="a1af0-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="a1af0-119">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-119">Low</span></span>        |
| [<span data-ttu-id="a1af0-120">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="a1af0-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="a1af0-121">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-121">Low</span></span>        |
| [<span data-ttu-id="a1af0-122">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="a1af0-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="a1af0-123">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-123">Low</span></span>        |
| [<span data-ttu-id="a1af0-124">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="a1af0-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="a1af0-125">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-125">Low</span></span>        |
| [<span data-ttu-id="a1af0-126">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="a1af0-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="a1af0-127">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-127">Low</span></span>        |
| [<span data-ttu-id="a1af0-128">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="a1af0-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="a1af0-129">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-129">Low</span></span>        |
| [<span data-ttu-id="a1af0-130">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="a1af0-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="a1af0-131">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-131">Low</span></span>        |
| [<span data-ttu-id="a1af0-132">Un pluarlizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="a1af0-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="a1af0-133">Basso</span><span class="sxs-lookup"><span data-stu-id="a1af0-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="a1af0-134">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="a1af0-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="a1af0-135">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="a1af0-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="a1af0-136">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-136">**Old behavior**</span></span>

<span data-ttu-id="a1af0-137">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="a1af0-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="a1af0-138">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="a1af0-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="a1af0-139">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a1af0-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="a1af0-140">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="a1af0-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="a1af0-141">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-141">**New behavior**</span></span>

<span data-ttu-id="a1af0-142">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="a1af0-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="a1af0-143">Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="a1af0-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="a1af0-144">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-144">**Why**</span></span>

<span data-ttu-id="a1af0-145">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="a1af0-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="a1af0-146">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-146">**Mitigations**</span></span>

<span data-ttu-id="a1af0-147">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="a1af0-147">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="a1af0-148">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="a1af0-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="a1af0-149">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="a1af0-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="a1af0-150">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-150">**Old behavior**</span></span>

<span data-ttu-id="a1af0-151">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="a1af0-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="a1af0-152">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="a1af0-153">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-153">**New behavior**</span></span>

<span data-ttu-id="a1af0-154">Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="a1af0-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="a1af0-155">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="a1af0-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="a1af0-156">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-156">**Why**</span></span>

<span data-ttu-id="a1af0-157">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="a1af0-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="a1af0-158">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-158">**Mitigations**</span></span>

<span data-ttu-id="a1af0-159">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="a1af0-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="a1af0-160">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="a1af0-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="a1af0-161">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="a1af0-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="a1af0-162">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-162">**Old behavior**</span></span>

<span data-ttu-id="a1af0-163">La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="a1af0-164">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-164">**New behavior**</span></span>

<span data-ttu-id="a1af0-165">La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="a1af0-166">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-166">**Why**</span></span>

<span data-ttu-id="a1af0-167">Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.</span><span class="sxs-lookup"><span data-stu-id="a1af0-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="a1af0-168">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-168">**Mitigations**</span></span>

<span data-ttu-id="a1af0-169">Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="a1af0-170">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="a1af0-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="a1af0-171">Rilevamento del problema #17751</span><span class="sxs-lookup"><span data-stu-id="a1af0-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="a1af0-172">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-172">**Old behavior**</span></span>

<span data-ttu-id="a1af0-173">Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="a1af0-174">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-174">**New behavior**</span></span>

<span data-ttu-id="a1af0-175">La proprietà shadow creata per convenzione è ora denominata `__id` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="a1af0-176">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-176">**Why**</span></span>

<span data-ttu-id="a1af0-177">Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="a1af0-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="a1af0-178">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-178">**Mitigations**</span></span>

<span data-ttu-id="a1af0-179">Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="a1af0-180">Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri</span><span class="sxs-lookup"><span data-stu-id="a1af0-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="a1af0-181">Rilevamento del problema #17306</span><span class="sxs-lookup"><span data-stu-id="a1af0-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="a1af0-182">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-182">**Old behavior**</span></span>

<span data-ttu-id="a1af0-183">Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.</span><span class="sxs-lookup"><span data-stu-id="a1af0-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="a1af0-184">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-184">**New behavior**</span></span>

<span data-ttu-id="a1af0-185">Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.</span><span class="sxs-lookup"><span data-stu-id="a1af0-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="a1af0-186">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-186">**Why**</span></span>

<span data-ttu-id="a1af0-187">Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.</span><span class="sxs-lookup"><span data-stu-id="a1af0-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="a1af0-188">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-188">**Mitigations**</span></span>

<span data-ttu-id="a1af0-189">I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento.</span><span class="sxs-lookup"><span data-stu-id="a1af0-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="a1af0-190">Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="a1af0-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="a1af0-191">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="a1af0-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="a1af0-192">Rilevamento del problema #17874</span><span class="sxs-lookup"><span data-stu-id="a1af0-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="a1af0-193">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-193">**Old behavior**</span></span>

<span data-ttu-id="a1af0-194">In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="a1af0-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="a1af0-195">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-195">**New behavior**</span></span>

<span data-ttu-id="a1af0-196">L'API precedente è stata rimossa e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="a1af0-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="a1af0-197">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-197">**Why**</span></span>

<span data-ttu-id="a1af0-198">Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="a1af0-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="a1af0-199">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-199">**Mitigations**</span></span>

<span data-ttu-id="a1af0-200">Usare la nuova API.</span><span class="sxs-lookup"><span data-stu-id="a1af0-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="a1af0-201">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="a1af0-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="a1af0-202">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="a1af0-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="a1af0-203">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-203">**Old behavior**</span></span>

<span data-ttu-id="a1af0-204">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="a1af0-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="a1af0-205">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-205">**New behavior**</span></span>

<span data-ttu-id="a1af0-206">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a1af0-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="a1af0-207">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-207">**Why**</span></span>

<span data-ttu-id="a1af0-208">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="a1af0-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="a1af0-209">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-209">**Mitigations**</span></span>

<span data-ttu-id="a1af0-210">Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="a1af0-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="a1af0-211">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="a1af0-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="a1af0-212">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="a1af0-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="a1af0-213">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-213">**Old behavior**</span></span>

<span data-ttu-id="a1af0-214">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="a1af0-215">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-215">**New behavior**</span></span>

<span data-ttu-id="a1af0-216">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="a1af0-217">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="a1af0-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="a1af0-218">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-218">**Why**</span></span>

<span data-ttu-id="a1af0-219">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="a1af0-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="a1af0-220">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="a1af0-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="a1af0-221">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-221">**Mitigations**</span></span>

<span data-ttu-id="a1af0-222">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="a1af0-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="a1af0-223">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="a1af0-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="a1af0-224">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="a1af0-224">Discriminators are read-only</span></span>

[<span data-ttu-id="a1af0-225">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="a1af0-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="a1af0-226">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-226">**Old behavior**</span></span>

<span data-ttu-id="a1af0-227">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="a1af0-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="a1af0-228">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-228">**New behavior**</span></span>

<span data-ttu-id="a1af0-229">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="a1af0-230">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-230">**Why**</span></span>

<span data-ttu-id="a1af0-231">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="a1af0-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="a1af0-232">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-232">**Mitigations**</span></span>

<span data-ttu-id="a1af0-233">Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="a1af0-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="a1af0-234">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="a1af0-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="a1af0-235">Rilevamento del problema #18903</span><span class="sxs-lookup"><span data-stu-id="a1af0-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="a1af0-236">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-236">**Old behavior**</span></span>

<span data-ttu-id="a1af0-237">È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core.</span><span class="sxs-lookup"><span data-stu-id="a1af0-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="a1af0-238">Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.</span><span class="sxs-lookup"><span data-stu-id="a1af0-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="a1af0-239">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-239">**New behavior**</span></span>

<span data-ttu-id="a1af0-240">Le API per la definizione della query sono deprecate.</span><span class="sxs-lookup"><span data-stu-id="a1af0-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="a1af0-241">Sono state introdotte nuove API specifiche del provider.</span><span class="sxs-lookup"><span data-stu-id="a1af0-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="a1af0-242">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-242">**Why**</span></span>

<span data-ttu-id="a1af0-243">Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="a1af0-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="a1af0-244">Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.</span><span class="sxs-lookup"><span data-stu-id="a1af0-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="a1af0-245">Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.</span><span class="sxs-lookup"><span data-stu-id="a1af0-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="a1af0-246">Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider In-Memory per le entità senza chiave (analogamente alle viste di database nei database relazionali).</span><span class="sxs-lookup"><span data-stu-id="a1af0-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="a1af0-247">Questa definizione consente di testare facilmente l'applicazione in base al database in memoria.</span><span class="sxs-lookup"><span data-stu-id="a1af0-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="a1af0-248">In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento.</span><span class="sxs-lookup"><span data-stu-id="a1af0-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="a1af0-249">Quindi abbiamo deciso di semplificare il concetto.</span><span class="sxs-lookup"><span data-stu-id="a1af0-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="a1af0-250">È stata creata una query di definizione basata su LINQ esclusiva per In-Memory provider e gestirli in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="a1af0-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="a1af0-251">Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="a1af0-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="a1af0-252">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-252">**Mitigations**</span></span>

<span data-ttu-id="a1af0-253">Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="a1af0-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="a1af0-254">Per il provider di In-Memory, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="a1af0-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="a1af0-255">EF specifico del provider. Metodi di funzioni generate per il provider InMemory</span><span class="sxs-lookup"><span data-stu-id="a1af0-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="a1af0-256">Rilevamento del problema #20294</span><span class="sxs-lookup"><span data-stu-id="a1af0-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="a1af0-257">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-257">**Old behavior**</span></span>

<span data-ttu-id="a1af0-258">EF specifico del provider. I metodi di funzioni contenevano l'implementazione per l'esecuzione del client, che ne consentiva l'esecuzione nel provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="a1af0-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="a1af0-259">Ad esempio, `EF.Functions.DateDiffDay` è un metodo specifico di SQL Server, che ha utilizzato il provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="a1af0-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="a1af0-260">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-260">**New behavior**</span></span>

<span data-ttu-id="a1af0-261">I metodi specifici del provider sono stati aggiornati per generare un'eccezione nel corpo del metodo per bloccare la valutazione sul lato client.</span><span class="sxs-lookup"><span data-stu-id="a1af0-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="a1af0-262">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-262">**Why**</span></span>

<span data-ttu-id="a1af0-263">I metodi specifici del provider vengono mappati a una funzione di database.</span><span class="sxs-lookup"><span data-stu-id="a1af0-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="a1af0-264">Il calcolo eseguito dalla funzione di database di cui è stato eseguito il mapping non può essere sempre replicato sul lato client in LINQ.</span><span class="sxs-lookup"><span data-stu-id="a1af0-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="a1af0-265">È possibile che il risultato del server risulti diverso quando si esegue lo stesso metodo nel client.</span><span class="sxs-lookup"><span data-stu-id="a1af0-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="a1af0-266">Poiché questi metodi vengono usati in LINQ per la conversione in funzioni di database specifiche, non devono essere valutati sul lato client.</span><span class="sxs-lookup"><span data-stu-id="a1af0-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="a1af0-267">Poiché il provider InMemory è un *database*diverso, questi metodi non sono disponibili per questo provider.</span><span class="sxs-lookup"><span data-stu-id="a1af0-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="a1af0-268">Il tentativo di eseguirli per il provider InMemory o qualsiasi altro provider che non traduce questi metodi genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="a1af0-269">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-269">**Mitigations**</span></span>

<span data-ttu-id="a1af0-270">Poiché non esiste alcun modo per simulare in modo accurato il comportamento delle funzioni di database, è consigliabile testare le query che li contengono sullo stesso tipo di database in produzione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="a1af0-271">IndexBuilder. HasName è ora obsoleto</span><span class="sxs-lookup"><span data-stu-id="a1af0-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="a1af0-272">Rilevamento del problema #21089</span><span class="sxs-lookup"><span data-stu-id="a1af0-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="a1af0-273">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-273">**Old behavior**</span></span>

<span data-ttu-id="a1af0-274">In precedenza era possibile definire un solo indice su un determinato set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="a1af0-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="a1af0-275">Il nome del database di un indice è stato configurato con IndexBuilder. HasName.</span><span class="sxs-lookup"><span data-stu-id="a1af0-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="a1af0-276">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-276">**New behavior**</span></span>

<span data-ttu-id="a1af0-277">Sono ora consentiti più indici nello stesso set o proprietà.</span><span class="sxs-lookup"><span data-stu-id="a1af0-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="a1af0-278">Questi indici sono ora distinti da un nome nel modello.</span><span class="sxs-lookup"><span data-stu-id="a1af0-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="a1af0-279">Per convenzione, il nome del modello viene utilizzato come nome del database. Tuttavia, può anche essere configurato in modo indipendente utilizzando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="a1af0-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="a1af0-280">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-280">**Why**</span></span>

<span data-ttu-id="a1af0-281">In futuro, vorremmo consentire l'abilitazione di indici sia ascendenti che decrescenti con regole di confronto diverse nello stesso set di proprietà.</span><span class="sxs-lookup"><span data-stu-id="a1af0-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="a1af0-282">Questa modifica consente di spostarsi in un altro passaggio della direzione.</span><span class="sxs-lookup"><span data-stu-id="a1af0-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="a1af0-283">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-283">**Mitigations**</span></span>

<span data-ttu-id="a1af0-284">Il codice precedentemente chiamato IndexBuilder. HasName deve essere aggiornato in modo da chiamare HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="a1af0-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="a1af0-285">Se il progetto include migrazioni generate prima della versione 2.0.0 di EF Core, è possibile ignorare l'avviso in tali file ed eliminarlo aggiungendo `#pragma warning disable 612, 618` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="a1af0-286">Un pluarlizer è ora incluso per i modelli di impalcatura decodificati</span><span class="sxs-lookup"><span data-stu-id="a1af0-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="a1af0-287">Rilevamento del problema #11160</span><span class="sxs-lookup"><span data-stu-id="a1af0-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="a1af0-288">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="a1af0-288">**Old behavior**</span></span>

<span data-ttu-id="a1af0-289">In precedenza era necessario installare un pacchetto pluralizer separato per plurali DbSet e i nomi di navigazione della raccolta e i nomi di tabella singolari quando scaffoding un DbContext e i tipi di entità reverse engineering uno schema di database.</span><span class="sxs-lookup"><span data-stu-id="a1af0-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="a1af0-290">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="a1af0-290">**New behavior**</span></span>

<span data-ttu-id="a1af0-291">EF Core ora include un pluralizer che usa la libreria [umanizzator](https://github.com/Humanizr/Humanizer) .</span><span class="sxs-lookup"><span data-stu-id="a1af0-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="a1af0-292">Si tratta della stessa libreria utilizzata da Visual Studio per consigliare i nomi delle variabili.</span><span class="sxs-lookup"><span data-stu-id="a1af0-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="a1af0-293">**Perché**</span><span class="sxs-lookup"><span data-stu-id="a1af0-293">**Why**</span></span>

<span data-ttu-id="a1af0-294">L'uso di forme plurali di parole per le proprietà della raccolta e i moduli singolari per i tipi e le proprietà di riferimento è idiomatiche in .NET.</span><span class="sxs-lookup"><span data-stu-id="a1af0-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="a1af0-295">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="a1af0-295">**Mitigations**</span></span>

<span data-ttu-id="a1af0-296">Per disabilitare pluralizer, utilizzare l' `--no-pluralize` opzione on `dotnet ef dbcontext scaffold` o l'opzione `-NoPluralize` on `Scaffold-DbContext` .</span><span class="sxs-lookup"><span data-stu-id="a1af0-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
