---
title: Funzionalità aggiuntive di Rilevamento modifiche-EF Core
description: Varie funzionalità e scenari che coinvolgono EF Core rilevamento delle modifiche
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 63d96227b6862e920d900a5cc3f1f85d7c6d85ac
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024433"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="cc9f8-103">Funzionalità aggiuntive di Rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="cc9f8-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="cc9f8-104">Questo documento illustra le varie funzionalità e gli scenari che coinvolgono il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="cc9f8-105">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="cc9f8-106">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="cc9f8-107">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span><span class="sxs-lookup"><span data-stu-id="cc9f8-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-versus-addasync"></a><span data-ttu-id="cc9f8-108">`Add` contro `AddAsync`</span><span class="sxs-lookup"><span data-stu-id="cc9f8-108">`Add` versus `AddAsync`</span></span>

<span data-ttu-id="cc9f8-109">Entity Framework Core (EF Core) fornisce metodi asincroni ogni volta che l'utilizzo di tale metodo può comportare un'interazione del database.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="cc9f8-110">Vengono inoltre forniti metodi sincroni per evitare overhead quando si utilizzano database che non supportano l'accesso asincrono ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="cc9f8-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> non accedono normalmente al database, poiché questi metodi intrinsecamente iniziano a tenere traccia delle entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="cc9f8-112">Tuttavia, alcune forme di generazione del valore _possono_ accedere al database per generare un valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="cc9f8-113">L'unico generatore di valori che esegue questa operazione e viene fornito con EF Core è <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="cc9f8-114">L'uso di questo generatore è insolito; non viene mai configurato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="cc9f8-115">Ciò significa che la maggior parte delle applicazioni deve usare `Add` e non `AddAsync` .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="cc9f8-116">Altri metodi simili, `Update` ad esempio, `Attach` e `Remove` non hanno overload asincroni perché non generano mai nuovi valori di chiave e quindi non devono mai accedere al database.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="cc9f8-117">`AddRange`, `UpdateRange`, `AttachRange` e `RemoveRange`</span><span class="sxs-lookup"><span data-stu-id="cc9f8-117">`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`</span></span>

<span data-ttu-id="cc9f8-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> e <xref:Microsoft.EntityFrameworkCore.DbContext> forniscono versioni alternative di `Add` , `Update` , `Attach` e `Remove` che accettano più istanze in un'unica chiamata.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="cc9f8-119">Questi metodi sono <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> rispettivamente,, <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-119">These methods are <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectively.</span></span>

<span data-ttu-id="cc9f8-120">Questi metodi vengono forniti come praticità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="cc9f8-121">L'uso di un metodo "range" ha la stessa funzionalità di più chiamate al metodo non di intervallo equivalente.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="cc9f8-122">Non esiste alcuna differenza di prestazioni significativa tra i due approcci.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="cc9f8-123">Si tratta di un metodo diverso da EF6, `AddRange` in cui ed `Add` entrambi chiamati automaticamente `DetectChanges` , ma la chiamata `Add` a più volte ha causato la chiamata di DetectChanges più volte anziché una volta.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-123">This is different from EF6, where `AddRange` and `Add` both automatically called `DetectChanges`, but calling `Add` multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="cc9f8-124">Questa operazione è stata resa `AddRange` più efficiente in EF6.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-124">This made `AddRange` more efficient in EF6.</span></span> <span data-ttu-id="cc9f8-125">In EF Core, nessuno di questi metodi chiama automaticamente `DetectChanges` .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-125">In EF Core, neither of these methods automatically call `DetectChanges`.</span></span>

## <a name="dbcontext-versus-dbset-methods"></a><span data-ttu-id="cc9f8-126">DbContext rispetto ai metodi DbSet</span><span class="sxs-lookup"><span data-stu-id="cc9f8-126">DbContext versus DbSet methods</span></span>

<span data-ttu-id="cc9f8-127">Molti metodi, tra cui `Add` , `Update` , `Attach` e `Remove` , hanno implementazioni sia in <xref:Microsoft.EntityFrameworkCore.DbSet%601> che in <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="cc9f8-128">Questi metodi hanno _esattamente lo stesso comportamento per i_ normali tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="cc9f8-129">Questo è dovuto al fatto che il tipo CLR dell'entità è mappato a un solo tipo di entità nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="cc9f8-130">Pertanto, il tipo CLR definisce in modo completo il punto in cui l'entità si integra nel modello e quindi il DbSet da utilizzare può essere determinato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="cc9f8-131">L'eccezione a questa regola è quando si usano tipi di entità di tipo condiviso, introdotti in EF Core 5,0, principalmente per entità di join molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="cc9f8-132">Quando si usa un tipo di entità di tipo condiviso, è necessario prima creare un DbSet per il tipo di modello EF Core in uso.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="cc9f8-133">`Add` `Update` `Attach` È quindi possibile usare metodi come,, e `Remove` in DbSet senza ambiguità in merito a quali EF Core tipo di modello è in uso.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="cc9f8-134">Per impostazione predefinita, i tipi di entità di tipo condiviso vengono utilizzati per le entità di join nelle relazioni molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="cc9f8-135">Un tipo di entità di tipo condiviso può anche essere configurato in modo esplicito per l'utilizzo in una relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="cc9f8-136">Il codice seguente, ad esempio, viene configurato `Dictionary<string, int>` come tipo di entità join:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="cc9f8-137">[Cambiando le chiavi esterne e le](xref:core/change-tracking/relationship-changes) esplorazioni viene illustrato come associare due entità monitorando una nuova istanza di entità di join.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="cc9f8-138">Il codice seguente esegue questa operazione per il `Dictionary<string, int>` tipo di entità di tipo condiviso usato per l'entità di join:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

<span data-ttu-id="cc9f8-139">Si noti che <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> viene usato per creare un DbSet per il `PostTag` tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="cc9f8-140">Questo DbSet può quindi essere usato per chiamare `Add` con la nuova istanza di entità di join.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cc9f8-141">Il tipo CLR utilizzato per i tipi di entità join per convenzione può cambiare nelle versioni future per migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="cc9f8-142">Non dipendere da un tipo di entità join specifico, a meno che non sia stato configurato in modo esplicito come avviene per `Dictionary<string, int>` nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-versus-field-access"></a><span data-ttu-id="cc9f8-143">Accesso a proprietà e campi</span><span class="sxs-lookup"><span data-stu-id="cc9f8-143">Property versus field access</span></span>

<span data-ttu-id="cc9f8-144">A partire da EF Core 3,0, l'accesso alle proprietà dell'entità utilizza per impostazione predefinita il campo sottostante della proprietà.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="cc9f8-145">Questa operazione è efficace ed evita l'attivazione di effetti collaterali dalla chiamata di getter e setter di proprietà.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="cc9f8-146">Ad esempio, questo è il modo in cui il caricamento lazy è in grado di evitare l'attivazione di cicli infiniti.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="cc9f8-147">Per ulteriori informazioni sulla configurazione dei campi di supporto nel modello, vedere la pagina relativa ai campi sottoposti a [backup](xref:core/modeling/backing-field) .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="cc9f8-148">A volte può essere utile per EF Core generare effetti collaterali quando modifica i valori delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="cc9f8-149">Ad esempio, quando si data binding alle entità, l'impostazione di una proprietà può generare notifiche per U.I.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="cc9f8-150">che non si verificano quando si imposta direttamente il campo.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="cc9f8-151">Questa operazione può essere eseguita cambiando <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> per:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="cc9f8-152">Tutti i tipi di entità nel modello utilizzando <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="cc9f8-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="cc9f8-153">Tutte le proprietà e le navigazioni di un tipo di entità specifico usando <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="cc9f8-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="cc9f8-154">Una proprietà specifica con <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="cc9f8-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="cc9f8-155">Una navigazione specifica con <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="cc9f8-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="cc9f8-156">Le modalità di accesso alle proprietà `Field` e `PreferField` determineranno EF Core accedere al valore della proprietà tramite il campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="cc9f8-157">Analogamente, `Property` e `PreferProperty` comporterà EF Core accedere al valore della proprietà tramite il metodo di richiamo e impostazione.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="cc9f8-158">Se `Field` `Property` si utilizzano o e EF Core non è in grado di accedere al valore rispettivamente tramite il metodo Get o Setter del campo o della proprietà, EF Core genererà un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="cc9f8-159">Ciò garantisce che EF Core usi sempre l'accesso al campo o alla proprietà quando si ritiene che sia.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="cc9f8-160">D'altra parte, `PreferField` `PreferProperty` se non è possibile usare l'accesso preferito, le modalità e eseguiranno il fallback rispettivamente all'uso della proprietà o del campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="cc9f8-161">`PreferField` è il valore predefinito di EF Core 3,0 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="cc9f8-162">Ciò significa che EF Core utilizzerà i campi ogni volta che è possibile, ma non avrà esito negativo se è necessario accedere a una proprietà tramite il getter o il setter.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="cc9f8-163">`FieldDuringConstruction` e `PreferFieldDuringConstruction` configurano EF core per l'uso dei campi di backup _solo quando si creano istanze di entità_.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="cc9f8-164">In questo modo è possibile eseguire le query senza effetti collaterali getter e setter, mentre le modifiche successive alle proprietà di EF Core provocheranno questi effetti collaterali.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="cc9f8-165">`PreferFieldDuringConstruction` è il valore predefinito precedente alla EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="cc9f8-166">Le diverse modalità di accesso alle proprietà sono riepilogate nella tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="cc9f8-167">PropertyAccessMode</span><span class="sxs-lookup"><span data-stu-id="cc9f8-167">PropertyAccessMode</span></span>              | <span data-ttu-id="cc9f8-168">Preferenza</span><span class="sxs-lookup"><span data-stu-id="cc9f8-168">Preference</span></span> | <span data-ttu-id="cc9f8-169">Preferenza per la creazione di entità</span><span class="sxs-lookup"><span data-stu-id="cc9f8-169">Preference creating entities</span></span> | <span data-ttu-id="cc9f8-170">Fallback</span><span class="sxs-lookup"><span data-stu-id="cc9f8-170">Fallback</span></span> | <span data-ttu-id="cc9f8-171">Fallback creazione di entità</span><span class="sxs-lookup"><span data-stu-id="cc9f8-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="cc9f8-172">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-172">Field</span></span>      | <span data-ttu-id="cc9f8-173">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-173">Field</span></span>                        | <span data-ttu-id="cc9f8-174">Genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="cc9f8-174">Throws</span></span>   | <span data-ttu-id="cc9f8-175">Genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="cc9f8-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="cc9f8-176">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-176">Property</span></span>   | <span data-ttu-id="cc9f8-177">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-177">Property</span></span>                     | <span data-ttu-id="cc9f8-178">Genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="cc9f8-178">Throws</span></span>   | <span data-ttu-id="cc9f8-179">Genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="cc9f8-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="cc9f8-180">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-180">Field</span></span>      | <span data-ttu-id="cc9f8-181">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-181">Field</span></span>                        | <span data-ttu-id="cc9f8-182">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-182">Property</span></span> | <span data-ttu-id="cc9f8-183">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="cc9f8-184">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-184">Property</span></span>   | <span data-ttu-id="cc9f8-185">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-185">Property</span></span>                     | <span data-ttu-id="cc9f8-186">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-186">Field</span></span>    | <span data-ttu-id="cc9f8-187">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="cc9f8-188">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-188">Property</span></span>   | <span data-ttu-id="cc9f8-189">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-189">Field</span></span>                        | <span data-ttu-id="cc9f8-190">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-190">Field</span></span>    | <span data-ttu-id="cc9f8-191">Genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="cc9f8-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="cc9f8-192">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-192">Property</span></span>   | <span data-ttu-id="cc9f8-193">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-193">Field</span></span>                        | <span data-ttu-id="cc9f8-194">Campo</span><span class="sxs-lookup"><span data-stu-id="cc9f8-194">Field</span></span>    | <span data-ttu-id="cc9f8-195">Proprietà</span><span class="sxs-lookup"><span data-stu-id="cc9f8-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="cc9f8-196">Valori temporanei</span><span class="sxs-lookup"><span data-stu-id="cc9f8-196">Temporary values</span></span>

<span data-ttu-id="cc9f8-197">EF Core crea valori di chiave temporanea durante il rilevamento di nuove entità che avranno valori di chiave reali generati dal database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="cc9f8-198">Per una panoramica del modo in cui vengono usati questi valori temporanei, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="cc9f8-199">Accesso ai valori temporanei</span><span class="sxs-lookup"><span data-stu-id="cc9f8-199">Accessing temporary values</span></span>

<span data-ttu-id="cc9f8-200">A partire da EF Core 3,0, i valori temporanei vengono archiviati in rilevamento modifiche e non vengono impostati direttamente sulle istanze di entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="cc9f8-201">Tuttavia, questi valori temporanei vengono esposti quando _si_ usano i vari meccanismi per [accedere alle entità registrate](xref:core/change-tracking/entity-entries).</span><span class="sxs-lookup"><span data-stu-id="cc9f8-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="cc9f8-202">Il codice seguente, ad esempio, accede a un valore temporaneo usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="cc9f8-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="cc9f8-203">L'output di questo codice è:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="cc9f8-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> può essere usato per verificare la presenza di valori temporanei.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="cc9f8-205">Modifica di valori temporanei</span><span class="sxs-lookup"><span data-stu-id="cc9f8-205">Manipulating temporary values</span></span>

<span data-ttu-id="cc9f8-206">A volte è utile usare in modo esplicito i valori temporanei.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="cc9f8-207">Ad esempio, è possibile creare una raccolta di nuove entità in un client Web e quindi serializzarle nuovamente nel server.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="cc9f8-208">I valori di chiave esterna rappresentano un modo per configurare le relazioni tra queste entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="cc9f8-209">Il codice seguente usa questo approccio per associare un grafico di nuove entità in base alla chiave esterna, consentendo allo stesso tempo la generazione di valori di chiave reali quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="cc9f8-210">Si noti che:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-210">Notice that:</span></span>

- <span data-ttu-id="cc9f8-211">I numeri negativi vengono utilizzati come valori di chiave temporanei. Questa operazione non è obbligatoria, ma è una convenzione comune per evitare conflitti di chiave.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="cc9f8-212">Alla `Post.BlogId` Proprietà FK viene assegnato lo stesso valore negativo del PK del Blog associato.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="cc9f8-213">I valori di PK sono contrassegnati come temporanei impostando il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> rilevamento di ogni entità.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="cc9f8-214">Questa operazione è necessaria perché viene considerato un valore di chiave reale per qualsiasi valore di chiave fornito dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="cc9f8-215">Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges si indica che i valori PK sono contrassegnati come temporanei e che i post sono associati ai Blog corretti, inclusa la correzione delle spostamenti:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="cc9f8-216">Dopo la chiamata a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , questi valori temporanei sono stati sostituiti dai valori reali generati dal database:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="cc9f8-217">Uso dei valori predefiniti</span><span class="sxs-lookup"><span data-stu-id="cc9f8-217">Working with default values</span></span>

<span data-ttu-id="cc9f8-218">EF Core consente a una proprietà di ottenere il relativo valore predefinito dal database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="cc9f8-219">Analogamente a quanto avviene con i valori di chiave generati, EF Core utilizzerà solo un valore predefinito del database se non è stato impostato in modo esplicito alcun valore.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="cc9f8-220">Si consideri, ad esempio, il tipo di entità seguente:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="cc9f8-221">La `ValidFrom` proprietà è configurata in modo da ottenere un valore predefinito dal database:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="cc9f8-222">Quando si inserisce un'entità di questo tipo, EF Core consente al database di generare il valore a meno che non sia stato impostato un valore esplicito.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="cc9f8-223">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="cc9f8-224">Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) viene mostrato che il primo token è stato `ValidFrom` generato dal database, mentre il secondo token usava il valore impostato in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="cc9f8-225">Per utilizzare i valori predefiniti del database, è necessario configurare il vincolo di valore predefinito per la colonna di database.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="cc9f8-226">Questa operazione viene eseguita automaticamente da EF Core migrazioni quando si usa <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> o <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> .</span><span class="sxs-lookup"><span data-stu-id="cc9f8-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="cc9f8-227">Assicurarsi di creare il vincolo predefinito sulla colonna in un altro modo quando non si usano migrazioni di EF Core.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="cc9f8-228">Uso delle proprietà Nullable</span><span class="sxs-lookup"><span data-stu-id="cc9f8-228">Using nullable properties</span></span>

<span data-ttu-id="cc9f8-229">EF Core è in grado di determinare se è stata impostata una proprietà confrontando il valore della proprietà con il valore predefinito CLR per quel tipo.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="cc9f8-230">Questa operazione funziona correttamente nella maggior parte dei casi, ma significa che non è possibile inserire in modo esplicito il valore predefinito CLR nel database.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="cc9f8-231">Si consideri ad esempio un'entità con una proprietà Integer:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="cc9f8-232">Se la proprietà è configurata con il valore predefinito del database-1:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="cc9f8-233">L'intenzione è che verrà usato il valore predefinito-1 ogni volta che un valore esplicito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="cc9f8-234">Tuttavia, l'impostazione del valore su 0 (valore predefinito CLR per numeri interi) non è distinguibile per EF Core di non impostare alcun valore. Ciò significa che non è possibile inserire 0 per questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="cc9f8-235">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="cc9f8-236">Si noti che l'istanza `Count` di in cui è stato impostato in modo esplicito su 0 ottiene comunque il valore predefinito dal database, che non è quello previsto.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="cc9f8-237">Un modo semplice per gestire questo problema consiste nel rendere `Count` Nullable la proprietà:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="cc9f8-238">Il valore predefinito di CLR è null, anziché 0, che indica che il valore 0 verrà inserito quando viene impostato in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="cc9f8-239">Uso dei campi di supporto Nullable</span><span class="sxs-lookup"><span data-stu-id="cc9f8-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="cc9f8-240">Questo modello di campo di supporto Nullable è supportato da EF Core 5,0 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="cc9f8-241">Il problema di rendere la proprietà Nullable che può non essere concettualmente nullable nel modello di dominio.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="cc9f8-242">La forzatura della proprietà come Nullable compromette pertanto il modello.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="cc9f8-243">A partire da EF Core 5,0, la proprietà può essere lasciata non nullable e solo il campo sottostante ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="cc9f8-244">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="cc9f8-245">Questo consente l'inserimento di CLR default (0) se la proprietà è impostata in modo esplicito su 0, senza dover esporre la proprietà come nullable nel modello di dominio.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="cc9f8-246">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="cc9f8-247">Campi di supporto Nullable per le proprietà bool</span><span class="sxs-lookup"><span data-stu-id="cc9f8-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="cc9f8-248">Questo modello è particolarmente utile quando si usano le proprietà bool con le impostazioni predefinite generate dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="cc9f8-249">Poiché il valore predefinito di CLR per `bool` è "false", significa che "false" non può essere inserito in modo esplicito con il modello normale.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="cc9f8-250">Si consideri, ad esempio, un `User` tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="cc9f8-251">La `IsAuthorized` proprietà è configurata con il valore predefinito del database "true":</span><span class="sxs-lookup"><span data-stu-id="cc9f8-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="cc9f8-252">La `IsAuthorized` proprietà può essere impostata su "true" o "false" in modo esplicito prima dell'inserimento. in caso contrario, il valore predefinito del database verrà utilizzato:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="cc9f8-253">L'output di SaveChanges quando si usa SQLite indica che per Mac viene usato il valore predefinito del database, mentre i valori espliciti sono impostati per Alice e Baxter:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="cc9f8-254">Solo impostazioni predefinite schema</span><span class="sxs-lookup"><span data-stu-id="cc9f8-254">Schema defaults only</span></span>

<span data-ttu-id="cc9f8-255">A volte è utile avere impostazioni predefinite nello schema del database creato da EF Core migrazioni senza EF Core mai usare questi valori per gli inserimenti.</span><span class="sxs-lookup"><span data-stu-id="cc9f8-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="cc9f8-256">Questa operazione può essere eseguita configurando la proprietà come <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cc9f8-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
