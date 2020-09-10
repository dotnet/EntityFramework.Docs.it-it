---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618684"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="6198c-103">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="6198c-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="6198c-104">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="6198c-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="6198c-105">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="6198c-105">Summary</span></span>

| <span data-ttu-id="6198c-106">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="6198c-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="6198c-107">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="6198c-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="6198c-108">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="6198c-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="6198c-109">Media</span><span class="sxs-lookup"><span data-stu-id="6198c-109">Medium</span></span>     |
| [<span data-ttu-id="6198c-110">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="6198c-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="6198c-111">Basso</span><span class="sxs-lookup"><span data-stu-id="6198c-111">Low</span></span>        |
| [<span data-ttu-id="6198c-112">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="6198c-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="6198c-113">Basso</span><span class="sxs-lookup"><span data-stu-id="6198c-113">Low</span></span>        |
| [<span data-ttu-id="6198c-114">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="6198c-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="6198c-115">Basso</span><span class="sxs-lookup"><span data-stu-id="6198c-115">Low</span></span>        |
| [<span data-ttu-id="6198c-116">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="6198c-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="6198c-117">Basso</span><span class="sxs-lookup"><span data-stu-id="6198c-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="6198c-118">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="6198c-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="6198c-119">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="6198c-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="6198c-120">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="6198c-120">**Old behavior**</span></span>

<span data-ttu-id="6198c-121">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="6198c-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="6198c-122">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="6198c-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="6198c-123">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="6198c-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="6198c-124">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="6198c-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="6198c-125">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="6198c-125">**New behavior**</span></span>

<span data-ttu-id="6198c-126">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="6198c-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="6198c-127">Questo corrisponde maggiormente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="6198c-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="6198c-128">**Perché**</span><span class="sxs-lookup"><span data-stu-id="6198c-128">**Why**</span></span>

<span data-ttu-id="6198c-129">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="6198c-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="6198c-130">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="6198c-130">**Mitigations**</span></span>

<span data-ttu-id="6198c-131">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="6198c-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="6198c-132">Obbligatorio per la navigazione da principale a dipendente con semantica diversa</span><span class="sxs-lookup"><span data-stu-id="6198c-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="6198c-133">Rilevamento del problema #17286</span><span class="sxs-lookup"><span data-stu-id="6198c-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="6198c-134">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="6198c-134">**Old behavior**</span></span>

<span data-ttu-id="6198c-135">È possibile configurare solo le navigazioni all'entità come richiesto.</span><span class="sxs-lookup"><span data-stu-id="6198c-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="6198c-136">Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.</span><span class="sxs-lookup"><span data-stu-id="6198c-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="6198c-137">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="6198c-137">**New behavior**</span></span>

<span data-ttu-id="6198c-138">Con il supporto aggiunto per i dipendenti richiesti, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.</span><span class="sxs-lookup"><span data-stu-id="6198c-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="6198c-139">`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:</span><span class="sxs-lookup"><span data-stu-id="6198c-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="6198c-140">**Perché**</span><span class="sxs-lookup"><span data-stu-id="6198c-140">**Why**</span></span>

<span data-ttu-id="6198c-141">Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="6198c-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="6198c-142">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="6198c-142">**Mitigations**</span></span>

<span data-ttu-id="6198c-143">Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="6198c-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="6198c-144">I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato</span><span class="sxs-lookup"><span data-stu-id="6198c-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="6198c-145">Rilevamento del problema #15289</span><span class="sxs-lookup"><span data-stu-id="6198c-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="6198c-146">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="6198c-146">**Old behavior**</span></span>

<span data-ttu-id="6198c-147">I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.</span><span class="sxs-lookup"><span data-stu-id="6198c-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="6198c-148">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="6198c-148">**New behavior**</span></span>

<span data-ttu-id="6198c-149">I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="6198c-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="6198c-150">**Perché**</span><span class="sxs-lookup"><span data-stu-id="6198c-150">**Why**</span></span>

<span data-ttu-id="6198c-151">Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.</span><span class="sxs-lookup"><span data-stu-id="6198c-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="6198c-152">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="6198c-152">**Mitigations**</span></span>

<span data-ttu-id="6198c-153">Per impedire che il generatore di valori venga chiamato, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.</span><span class="sxs-lookup"><span data-stu-id="6198c-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="6198c-154">IMigrationsModelDiffer USA ora IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="6198c-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="6198c-155">Rilevamento del problema #20305</span><span class="sxs-lookup"><span data-stu-id="6198c-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="6198c-156">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="6198c-156">**Old behavior**</span></span>

<span data-ttu-id="6198c-157">`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .</span><span class="sxs-lookup"><span data-stu-id="6198c-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="6198c-158">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="6198c-158">**New behavior**</span></span>

<span data-ttu-id="6198c-159">`IMigrationsModelDiffer` API USA ora `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="6198c-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="6198c-160">Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="6198c-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="6198c-161">**Perché**</span><span class="sxs-lookup"><span data-stu-id="6198c-161">**Why**</span></span>

<span data-ttu-id="6198c-162">`IRelationalModel` rappresentazione appena aggiunta dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="6198c-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="6198c-163">Il suo utilizzo per individuare le differenze è più rapido e accurato.</span><span class="sxs-lookup"><span data-stu-id="6198c-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="6198c-164">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="6198c-164">**Mitigations**</span></span>

<span data-ttu-id="6198c-165">Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :</span><span class="sxs-lookup"><span data-stu-id="6198c-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="6198c-166">Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="6198c-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="6198c-167">I discriminatori sono di sola lettura</span><span class="sxs-lookup"><span data-stu-id="6198c-167">Discriminators are read-only</span></span>

[<span data-ttu-id="6198c-168">Rilevamento del problema #21154</span><span class="sxs-lookup"><span data-stu-id="6198c-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="6198c-169">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="6198c-169">**Old behavior**</span></span>

<span data-ttu-id="6198c-170">È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="6198c-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="6198c-171">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="6198c-171">**New behavior**</span></span>

<span data-ttu-id="6198c-172">Nel caso precedente verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="6198c-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="6198c-173">**Perché**</span><span class="sxs-lookup"><span data-stu-id="6198c-173">**Why**</span></span>

<span data-ttu-id="6198c-174">EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente che può causare un comportamento imprevisto.</span><span class="sxs-lookup"><span data-stu-id="6198c-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="6198c-175">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="6198c-175">**Mitigations**</span></span>

<span data-ttu-id="6198c-176">Se la modifica del valore del discriminatore è necessaria e il contesto verrà eliminato immediatamente dopo la chiamata `SaveChanges` al discriminatore può essere reso modificabile:</span><span class="sxs-lookup"><span data-stu-id="6198c-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
