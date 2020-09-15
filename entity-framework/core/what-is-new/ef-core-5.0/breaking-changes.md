---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070796"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="16969-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="16969-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="16969-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="16969-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="16969-105">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="16969-105">Summary</span></span>

| <span data-ttu-id="16969-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="16969-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="16969-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="16969-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="16969-108">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="16969-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="16969-109">Media</span><span class="sxs-lookup"><span data-stu-id="16969-109">Medium</span></span>     |
| [<span data-ttu-id="16969-110">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="16969-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="16969-111">Media</span><span class="sxs-lookup"><span data-stu-id="16969-111">Medium</span></span>     |
| [<span data-ttu-id="16969-112">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="16969-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="16969-113">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-113">Low</span></span>        |
| [<span data-ttu-id="16969-114">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="16969-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="16969-115">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-115">Low</span></span>        |
| [<span data-ttu-id="16969-116">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="16969-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="16969-117">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-117">Low</span></span>        |
| <span data-ttu-id="16969-118">[Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="16969-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="16969-119">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-119">Low</span></span>        |
| [<span data-ttu-id="16969-120">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="16969-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="16969-121">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-121">Low</span></span>        |
| [<span data-ttu-id="16969-122">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="16969-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="16969-123">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-123">Low</span></span>        |
| [<span data-ttu-id="16969-124">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="16969-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="16969-125">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-125">Low</span></span>        |
| [<span data-ttu-id="16969-126">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="16969-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="16969-127">Basso</span><span class="sxs-lookup"><span data-stu-id="16969-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="16969-128">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="16969-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="16969-129">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="16969-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="16969-130">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-130">**Old behavior**</span></span>

<span data-ttu-id="16969-131">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="16969-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="16969-132">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="16969-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="16969-133">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="16969-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="16969-134">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="16969-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="16969-135">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-135">**New behavior**</span></span>

<span data-ttu-id="16969-136">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="16969-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="16969-137">Questa API corrisponde più strettamente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="16969-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="16969-138">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-138">**Why**</span></span>

<span data-ttu-id="16969-139">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="16969-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="16969-140">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-140">**Mitigations**</span></span>

<span data-ttu-id="16969-141">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="16969-141">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="16969-142">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="16969-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="16969-143">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="16969-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="16969-144">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-144">**Old behavior**</span></span>

<span data-ttu-id="16969-145">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="16969-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="16969-146">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="16969-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="16969-147">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-147">**New behavior**</span></span>

<span data-ttu-id="16969-148">Con il supporto aggiunto per i dipendenti obbligatori, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="16969-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="16969-149">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="16969-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="16969-150">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-150">**Why**</span></span>

<span data-ttu-id="16969-151">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="16969-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="16969-152">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-152">**Mitigations**</span></span>

<span data-ttu-id="16969-153">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="16969-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="16969-154">Cosmos: la chiave di partizione è stata aggiunta alla chiave primaria</span><span class="sxs-lookup"><span data-stu-id="16969-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="16969-155">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="16969-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="16969-156">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-156">**Old behavior**</span></span>

<span data-ttu-id="16969-157">La proprietà della chiave di partizione è stata aggiunta solo alla chiave alternativa inclusa `id` .</span><span class="sxs-lookup"><span data-stu-id="16969-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="16969-158">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-158">**New behavior**</span></span>

<span data-ttu-id="16969-159">La proprietà chiave di partizione viene ora aggiunta alla chiave primaria per convenzione.</span><span class="sxs-lookup"><span data-stu-id="16969-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="16969-160">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-160">**Why**</span></span>

<span data-ttu-id="16969-161">Questa modifica rende il modello più allineato con Azure Cosmos DB semantica e migliora le prestazioni di `Find` e di alcune query.</span><span class="sxs-lookup"><span data-stu-id="16969-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="16969-162">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-162">**Mitigations**</span></span>

<span data-ttu-id="16969-163">Per impedire l'aggiunta della proprietà della chiave di partizione alla chiave primaria, configurarla in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="16969-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="16969-164">Cosmos: `id` proprietà rinominata in `__id`</span><span class="sxs-lookup"><span data-stu-id="16969-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="16969-165">Rilevamento del problema #17751</span><span class="sxs-lookup"><span data-stu-id="16969-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="16969-166">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-166">**Old behavior**</span></span>

<span data-ttu-id="16969-167">Anche la proprietà shadow mappata alla `id` Proprietà JSON è denominata `id` .</span><span class="sxs-lookup"><span data-stu-id="16969-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="16969-168">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-168">**New behavior**</span></span>

<span data-ttu-id="16969-169">La proprietà shadow creata per convenzione è ora denominata `__id` .</span><span class="sxs-lookup"><span data-stu-id="16969-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="16969-170">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-170">**Why**</span></span>

<span data-ttu-id="16969-171">Questa modifica rende meno probabile che la `id` proprietà si scontri con una proprietà esistente nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="16969-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="16969-172">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-172">**Mitigations**</span></span>

<span data-ttu-id="16969-173">Per tornare al comportamento 3. x, configurare la `id` Proprietà in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="16969-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="16969-174">Cosmos: byte [] è ora archiviato come stringa Base64 anziché come matrice di numeri</span><span class="sxs-lookup"><span data-stu-id="16969-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="16969-175">Rilevamento del problema #17306</span><span class="sxs-lookup"><span data-stu-id="16969-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="16969-176">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-176">**Old behavior**</span></span>

<span data-ttu-id="16969-177">Le proprietà di tipo byte [] sono state archiviate come matrice di numeri.</span><span class="sxs-lookup"><span data-stu-id="16969-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="16969-178">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-178">**New behavior**</span></span>

<span data-ttu-id="16969-179">Le proprietà di tipo byte [] sono ora archiviate come stringa Base64.</span><span class="sxs-lookup"><span data-stu-id="16969-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="16969-180">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-180">**Why**</span></span>

<span data-ttu-id="16969-181">Questa rappresentazione di byte [] è allineata meglio con le aspettative ed è il comportamento predefinito delle principali librerie di serializzazione JSON.</span><span class="sxs-lookup"><span data-stu-id="16969-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="16969-182">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-182">**Mitigations**</span></span>

<span data-ttu-id="16969-183">I dati esistenti archiviati come matrici di numeri verranno comunque sottoposti a query correttamente, ma attualmente non è disponibile un modo supportato per modificare il comportamento di inserimento.</span><span class="sxs-lookup"><span data-stu-id="16969-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="16969-184">Se questa limitazione blocca lo scenario, commentare il [problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="16969-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="16969-185">Cosmos: GetPropertyName e SetPropertyName sono stati rinominati</span><span class="sxs-lookup"><span data-stu-id="16969-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="16969-186">Rilevamento del problema #17874</span><span class="sxs-lookup"><span data-stu-id="16969-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="16969-187">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-187">**Old behavior**</span></span>

<span data-ttu-id="16969-188">In precedenza venivano chiamati i metodi `GetPropertyName` di estensione e `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="16969-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="16969-189">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-189">**New behavior**</span></span>

<span data-ttu-id="16969-190">L'API precedente è obsoleta e sono stati aggiunti nuovi metodi: `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="16969-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="16969-191">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-191">**Why**</span></span>

<span data-ttu-id="16969-192">Questa modifica rimuove l'ambiguità relativa alla configurazione di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="16969-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="16969-193">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-193">**Mitigations**</span></span>

<span data-ttu-id="16969-194">Usare la nuova API o sospendere temporaneamente gli avvisi obsoleti.</span><span class="sxs-lookup"><span data-stu-id="16969-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="16969-195">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="16969-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="16969-196">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="16969-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="16969-197">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-197">**Old behavior**</span></span>

<span data-ttu-id="16969-198">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="16969-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="16969-199">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-199">**New behavior**</span></span>

<span data-ttu-id="16969-200">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="16969-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="16969-201">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-201">**Why**</span></span>

<span data-ttu-id="16969-202">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="16969-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="16969-203">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-203">**Mitigations**</span></span>

<span data-ttu-id="16969-204">Per evitare che venga chiamato il generatore di valori, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="16969-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="16969-205">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="16969-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="16969-206">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="16969-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="16969-207">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-207">**Old behavior**</span></span>

<span data-ttu-id="16969-208">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="16969-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="16969-209">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-209">**New behavior**</span></span>

<span data-ttu-id="16969-210">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="16969-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="16969-211">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="16969-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="16969-212">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-212">**Why**</span></span>

<span data-ttu-id="16969-213">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="16969-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="16969-214">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="16969-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="16969-215">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-215">**Mitigations**</span></span>

<span data-ttu-id="16969-216">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="16969-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="16969-217">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="16969-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="16969-218">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="16969-218">Discriminators are read-only</span></span>

[<span data-ttu-id="16969-219">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="16969-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="16969-220">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-220">**Old behavior**</span></span>

<span data-ttu-id="16969-221">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="16969-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="16969-222">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-222">**New behavior**</span></span>

<span data-ttu-id="16969-223">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="16969-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="16969-224">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-224">**Why**</span></span>

<span data-ttu-id="16969-225">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente, che potrebbe causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="16969-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="16969-226">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-226">**Mitigations**</span></span>

<span data-ttu-id="16969-227">Se è necessario modificare il valore del discriminatore e il contesto verrà eliminato immediatamente dopo la chiamata a `SaveChanges` , il discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="16969-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="16969-228">La definizione della query viene sostituita con metodi specifici del provider</span><span class="sxs-lookup"><span data-stu-id="16969-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="16969-229">Rilevamento del problema #18903</span><span class="sxs-lookup"><span data-stu-id="16969-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="16969-230">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="16969-230">**Old behavior**</span></span>

<span data-ttu-id="16969-231">È stato eseguito il mapping dei tipi di entità alla definizione di query a livello di core.</span><span class="sxs-lookup"><span data-stu-id="16969-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="16969-232">Ogni volta che il tipo di entità è stato usato nella radice della query del tipo di entità è stato sostituito dalla query di definizione per qualsiasi provider.</span><span class="sxs-lookup"><span data-stu-id="16969-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="16969-233">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="16969-233">**New behavior**</span></span>

<span data-ttu-id="16969-234">Le API per la definizione della query sono deprecate.</span><span class="sxs-lookup"><span data-stu-id="16969-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="16969-235">Sono state introdotte nuove API specifiche del provider.</span><span class="sxs-lookup"><span data-stu-id="16969-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="16969-236">**Perché**</span><span class="sxs-lookup"><span data-stu-id="16969-236">**Why**</span></span>

<span data-ttu-id="16969-237">Durante la definizione delle query sono state implementate come query di sostituzione ogni volta che nella query viene utilizzata la radice della query, si sono verificati alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="16969-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="16969-238">Se la definizione della query è la proiezione del tipo di entità mediante `new { ... }` `Select` il metodo in, l'identificazione di come un'entità richiede un lavoro aggiuntivo e renderla incoerente con il modo in cui EF Core considera i tipi nominali nella query.</span><span class="sxs-lookup"><span data-stu-id="16969-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="16969-239">Per i provider relazionali `FromSql` è ancora necessario passare la stringa SQL in formato di espressione LINQ.</span><span class="sxs-lookup"><span data-stu-id="16969-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="16969-240">Inizialmente la definizione di query è stata introdotta come visualizzazioni lato client da usare con il provider in memoria per entità senza chiave (analogamente alle viste di database nei database relazionali).</span><span class="sxs-lookup"><span data-stu-id="16969-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="16969-241">Questa definizione consente di testare facilmente l'applicazione in base al database in memoria.</span><span class="sxs-lookup"><span data-stu-id="16969-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="16969-242">In seguito, sono diventati ampiamente applicabili, che era utile, ma ha reso incoerente e difficile comprendere il comportamento.</span><span class="sxs-lookup"><span data-stu-id="16969-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="16969-243">Quindi abbiamo deciso di semplificare il concetto.</span><span class="sxs-lookup"><span data-stu-id="16969-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="16969-244">È stata creata una query di definizione basata su LINQ esclusiva per il provider in memoria e gestita in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="16969-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="16969-245">Per ulteriori informazioni, [vedere questo problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="16969-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="16969-246">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="16969-246">**Mitigations**</span></span>

<span data-ttu-id="16969-247">Per i provider relazionali, utilizzare `ToSqlQuery` il metodo in `OnModelCreating` e passare una stringa SQL da utilizzare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="16969-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="16969-248">Per il provider in memoria, usare il `ToInMemoryQuery` metodo in `OnModelCreating` e passare una query LINQ da usare per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="16969-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
