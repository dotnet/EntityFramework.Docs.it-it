---
title: Tipi di entità di proprietà-EF Core
description: Come configurare i tipi di entità o le aggregazioni di proprietà quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 0cd6bfd25d4462509a3e6c112b892d652d29e45e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128628"
---
# <a name="owned-entity-types"></a><span data-ttu-id="afdc2-103">Tipi di entità di proprietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-103">Owned Entity Types</span></span>

<span data-ttu-id="afdc2-104">EF Core consente di modellare i tipi di entità che possono essere visualizzati solo nelle proprietà di navigazione di altri tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="afdc2-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="afdc2-105">Questi sono denominati _tipi di entità di proprietà_.</span><span class="sxs-lookup"><span data-stu-id="afdc2-105">These are called _owned entity types_.</span></span> <span data-ttu-id="afdc2-106">L'entità che contiene un tipo di entità di proprietà è il _proprietario_.</span><span class="sxs-lookup"><span data-stu-id="afdc2-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="afdc2-107">Le entità di proprietà sono essenzialmente parte del proprietario e non possono esistere senza di essa, sono concettualmente simili a quelle delle [aggregazioni](https://martinfowler.com/bliki/DDD_Aggregate.html).</span><span class="sxs-lookup"><span data-stu-id="afdc2-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="afdc2-108">Ciò significa che l'entità di proprietà è per definizione sul lato dipendente della relazione con il proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="afdc2-109">Configurazione esplicita</span><span class="sxs-lookup"><span data-stu-id="afdc2-109">Explicit configuration</span></span>

<span data-ttu-id="afdc2-110">I tipi di entità di proprietà non vengono mai inclusi da EF Core nel modello per convenzione.</span><span class="sxs-lookup"><span data-stu-id="afdc2-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="afdc2-111">È possibile usare il `OwnsOne` metodo in `OnModelCreating` o annotare il tipo con `OwnedAttribute` per configurare il tipo come tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` to configure the type as an owned type.</span></span>

<span data-ttu-id="afdc2-112">In questo esempio `StreetAddress` è un tipo senza proprietà Identity.</span><span class="sxs-lookup"><span data-stu-id="afdc2-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="afdc2-113">Viene usato come proprietà del tipo Order per specificare l'indirizzo di spedizione per uno specifico ordine.</span><span class="sxs-lookup"><span data-stu-id="afdc2-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="afdc2-114">`OwnedAttribute`Quando si fa riferimento a un altro tipo di entità, è possibile usare per considerarlo come un'entità di proprietà:</span><span class="sxs-lookup"><span data-stu-id="afdc2-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="afdc2-115">È anche possibile usare il `OwnsOne` metodo in `OnModelCreating` per specificare che la `ShippingAddress` proprietà è un'entità di proprietà del tipo di `Order` entità e per configurare facet aggiuntivi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="afdc2-116">Se la `ShippingAddress` proprietà è privata nel `Order` tipo, è possibile usare la versione in formato stringa del `OwnsOne` Metodo:</span><span class="sxs-lookup"><span data-stu-id="afdc2-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="afdc2-117">Il modello precedente è mappato allo schema di database seguente:</span><span class="sxs-lookup"><span data-stu-id="afdc2-117">The model above is mapped to the following database schema:</span></span>

![Sceenshot del modello di database per l'entità che contiene il riferimento di proprietà](_static/owned-entities-ownsone.png)

<span data-ttu-id="afdc2-119">Per ulteriori informazioni sul contesto, vedere il [progetto di esempio completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) .</span><span class="sxs-lookup"><span data-stu-id="afdc2-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

> [!TIP]
> <span data-ttu-id="afdc2-120">Il tipo di entità di proprietà può essere contrassegnato come obbligatorio. per ulteriori informazioni, vedere [dipendenti uno-a-uno richiesti](xref:core/modeling/relationships#one-to-one) .</span><span class="sxs-lookup"><span data-stu-id="afdc2-120">The owned entity type can be marked as required, see [Required one-to-one dependents](xref:core/modeling/relationships#one-to-one) for more information.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="afdc2-121">Chiavi implicite</span><span class="sxs-lookup"><span data-stu-id="afdc2-121">Implicit keys</span></span>

<span data-ttu-id="afdc2-122">I tipi di proprietà configurati con `OwnsOne` o individuati tramite un'esplorazione dei riferimenti hanno sempre una relazione uno-a-uno con il proprietario, pertanto non necessitano di valori di chiave personalizzati perché i valori di chiave esterna sono univoci.</span><span class="sxs-lookup"><span data-stu-id="afdc2-122">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="afdc2-123">Nell'esempio precedente, non è `StreetAddress` necessario che il tipo definisca una proprietà chiave.</span><span class="sxs-lookup"><span data-stu-id="afdc2-123">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>

<span data-ttu-id="afdc2-124">Per comprendere il modo in cui EF Core tiene traccia di questi oggetti, è utile sapere che una chiave primaria viene creata come [proprietà shadow](xref:core/modeling/shadow-properties) per il tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-124">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="afdc2-125">Il valore della chiave di un'istanza del tipo di proprietà sarà uguale al valore della chiave dell'istanza del proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-125">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="afdc2-126">Raccolte di tipi di proprietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-126">Collections of owned types</span></span>

<span data-ttu-id="afdc2-127">Per configurare una raccolta di tipi di proprietà `OwnsMany` , utilizzare in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="afdc2-127">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="afdc2-128">I tipi di proprietà necessitano di una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="afdc2-128">Owned types need a primary key.</span></span> <span data-ttu-id="afdc2-129">Se non sono presenti proprietà valide dei candidati sul tipo .NET, EF Core possibile provare a crearne una.</span><span class="sxs-lookup"><span data-stu-id="afdc2-129">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="afdc2-130">Tuttavia, quando i tipi di proprietà vengono definiti tramite una raccolta, non è sufficiente creare una proprietà shadow da utilizzare come chiave esterna nel proprietario e la chiave primaria dell'istanza di proprietà, come per `OwnsOne` : possono essere presenti più istanze di tipo di proprietà per ogni proprietario e, di conseguenza, la chiave del proprietario non è sufficiente per fornire un'identità univoca per ogni istanza di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-130">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="afdc2-131">Le due soluzioni più semplici sono:</span><span class="sxs-lookup"><span data-stu-id="afdc2-131">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="afdc2-132">Definizione di una chiave primaria surrogata in una nuova proprietà indipendente dalla chiave esterna che punta al proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-132">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="afdc2-133">I valori contenuti devono essere univoci in tutti i proprietari (ad esempio, se il padre {1} ha {1} un figlio, l'elemento padre {2} non può avere un elemento figlio {1} ), quindi il valore non ha un significato intrinseco.</span><span class="sxs-lookup"><span data-stu-id="afdc2-133">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="afdc2-134">Poiché la chiave esterna non fa parte della chiave primaria, i relativi valori possono essere modificati, quindi è possibile spostare un elemento figlio da un elemento padre a un altro, ma in genere viene eseguita la semantica di aggregazione.</span><span class="sxs-lookup"><span data-stu-id="afdc2-134">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="afdc2-135">Uso della chiave esterna e di una proprietà aggiuntiva come chiave composta.</span><span class="sxs-lookup"><span data-stu-id="afdc2-135">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="afdc2-136">Il valore aggiuntivo della proprietà deve ora essere univoco solo per un determinato elemento padre (pertanto, se il padre {1} ha un figlio {1,1} , l'elemento padre {2} può ancora avere figlio {2,1} ).</span><span class="sxs-lookup"><span data-stu-id="afdc2-136">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="afdc2-137">Rendendo la parte chiave esterna della chiave primaria, la relazione tra il proprietario e l'entità di proprietà diventa non modificabile e riflette meglio la semantica di aggregazione.</span><span class="sxs-lookup"><span data-stu-id="afdc2-137">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="afdc2-138">Per impostazione predefinita, EF Core esegue questa operazione.</span><span class="sxs-lookup"><span data-stu-id="afdc2-138">This is what EF Core does by default.</span></span>

<span data-ttu-id="afdc2-139">In questo esempio verrà usata la `Distributor` classe.</span><span class="sxs-lookup"><span data-stu-id="afdc2-139">In this example we'll use the `Distributor` class.</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="afdc2-140">Per impostazione predefinita, la chiave primaria utilizzata per il tipo di proprietà a cui viene fatto riferimento tramite la `ShippingCenters` proprietà di navigazione sarà `("DistributorId", "Id")` dove `"DistributorId"` è l'FK ed `"Id"` è un `int` valore univoco.</span><span class="sxs-lookup"><span data-stu-id="afdc2-140">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="afdc2-141">Per configurare una chiamata di chiave primaria diversa `HasKey` .</span><span class="sxs-lookup"><span data-stu-id="afdc2-141">To configure a different primary key call `HasKey`.</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

<span data-ttu-id="afdc2-142">Il modello precedente è mappato allo schema di database seguente:</span><span class="sxs-lookup"><span data-stu-id="afdc2-142">The model above is mapped to the following database schema:</span></span>

![Sceenshot del modello di database per l'entità che contiene la raccolta di proprietà](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="afdc2-144">Mapping dei tipi di proprietà con suddivisione della tabella</span><span class="sxs-lookup"><span data-stu-id="afdc2-144">Mapping owned types with table splitting</span></span>

<span data-ttu-id="afdc2-145">Quando si usano database relazionali, per impostazione predefinita i tipi di proprietà di riferimento vengono mappati alla stessa tabella del proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-145">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="afdc2-146">Questa operazione richiede la suddivisione della tabella in due: alcune colonne verranno utilizzate per archiviare i dati del proprietario e alcune colonne verranno utilizzate per archiviare i dati dell'entità di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-146">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="afdc2-147">Si tratta di una funzionalità comune nota come [suddivisione della tabella](xref:core/modeling/table-splitting).</span><span class="sxs-lookup"><span data-stu-id="afdc2-147">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="afdc2-148">Per impostazione predefinita, EF Core assegna un nome alle colonne del database per le proprietà del tipo di entità di proprietà che segue il modello _Navigation_OwnedEntityProperty_.</span><span class="sxs-lookup"><span data-stu-id="afdc2-148">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="afdc2-149">Pertanto, le `StreetAddress` proprietà verranno visualizzate nella tabella ' Orders ' con i nomi ' ShippingAddress_Street ' è ShippingAddress_City '.</span><span class="sxs-lookup"><span data-stu-id="afdc2-149">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="afdc2-150">È possibile utilizzare il `HasColumnName` metodo per rinominare le colonne.</span><span class="sxs-lookup"><span data-stu-id="afdc2-150">You can use the `HasColumnName` method to rename those columns.</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="afdc2-151">La maggior parte dei normali metodi di configurazione del tipo di entità come [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) può essere chiamata nello stesso modo.</span><span class="sxs-lookup"><span data-stu-id="afdc2-151">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="afdc2-152">Condivisione dello stesso tipo .NET tra più tipi di proprietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-152">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="afdc2-153">Un tipo di entità di proprietà può essere dello stesso tipo .NET di un altro tipo di entità di proprietà, pertanto il tipo .NET potrebbe non essere sufficiente per identificare un tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-153">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="afdc2-154">In questi casi, la proprietà che punta dal proprietario all'entità di proprietà diventa la _navigazione che definisce_ il tipo di entità di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-154">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="afdc2-155">Dal punto di vista del EF Core, la navigazione di definizione fa parte dell'identità del tipo insieme al tipo .NET.</span><span class="sxs-lookup"><span data-stu-id="afdc2-155">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="afdc2-156">Ad esempio, nella classe seguente `ShippingAddress` e `BillingAddress` sono entrambi dello stesso tipo .NET, `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="afdc2-156">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="afdc2-157">Per comprendere in che modo EF Core distinguere le istanze rilevate di questi oggetti, può essere utile pensare che la navigazione di definizione sia diventata parte della chiave dell'istanza insieme al valore della chiave del proprietario e al tipo .NET del tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-157">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="afdc2-158">Tipi di proprietà annidati</span><span class="sxs-lookup"><span data-stu-id="afdc2-158">Nested owned types</span></span>

<span data-ttu-id="afdc2-159">In questo esempio `OrderDetails` è proprietario di `BillingAddress` e `ShippingAddress` , che sono entrambi `StreetAddress` tipi.</span><span class="sxs-lookup"><span data-stu-id="afdc2-159">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="afdc2-160">Quindi `OrderDetails` è di proprietà del tipo `DetailedOrder`.</span><span class="sxs-lookup"><span data-stu-id="afdc2-160">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="afdc2-161">Ogni navigazione a un tipo di proprietà definisce un tipo di entità separato con configurazione completamente indipendente.</span><span class="sxs-lookup"><span data-stu-id="afdc2-161">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="afdc2-162">Oltre ai tipi di proprietà annidati, un tipo di proprietà può fare riferimento a un'entità regolare che può essere il proprietario o un'altra entità purché l'entità di proprietà si trovi sul lato dipendente.</span><span class="sxs-lookup"><span data-stu-id="afdc2-162">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="afdc2-163">Questa funzionalità imposta i tipi di entità di proprietà oltre ai tipi complessi in EF6.</span><span class="sxs-lookup"><span data-stu-id="afdc2-163">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="afdc2-164">Configurazione di tipi di proprietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-164">Configuring owned types</span></span>

<span data-ttu-id="afdc2-165">È possibile concatenare il `OwnsOne` metodo in una chiamata Fluent per configurare questo modello:</span><span class="sxs-lookup"><span data-stu-id="afdc2-165">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="afdc2-166">Si noti la `WithOwner` chiamata utilizzata per definire la proprietà di navigazione che fa riferimento al proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-166">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="afdc2-167">Per definire una navigazione al tipo di entità Owner che non fa parte della relazione di proprietà `WithOwner()` , è necessario chiamare senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="afdc2-167">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="afdc2-168">È anche possibile ottenere questo risultato usando `OwnedAttribute` sia in che in `OrderDetails` `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="afdc2-168">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="afdc2-169">Si noti inoltre la `Navigation` chiamata a.</span><span class="sxs-lookup"><span data-stu-id="afdc2-169">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="afdc2-170">In EFCore 5,0, le proprietà di navigazione ai tipi di proprietà possono essere configurate ulteriormente [come per le proprietà di navigazione non di proprietà](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="afdc2-170">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

<span data-ttu-id="afdc2-171">Il modello precedente è mappato allo schema di database seguente:</span><span class="sxs-lookup"><span data-stu-id="afdc2-171">The model above is mapped to the following database schema:</span></span>

![Screenshot del modello di database per l'entità che contiene riferimenti di proprietà annidati](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="afdc2-173">Archiviazione di tipi di proprietà in tabelle separate</span><span class="sxs-lookup"><span data-stu-id="afdc2-173">Storing owned types in separate tables</span></span>

<span data-ttu-id="afdc2-174">A differenza dei tipi complessi EF6, inoltre, i tipi di proprietà possono essere archiviati in una tabella separata dal proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-174">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="afdc2-175">Per eseguire l'override della convenzione che esegue il mapping di un tipo di proprietà alla stessa tabella del proprietario, è possibile chiamare semplicemente `ToTable` e fornire un nome di tabella diverso.</span><span class="sxs-lookup"><span data-stu-id="afdc2-175">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="afdc2-176">Nell'esempio seguente viene mappato `OrderDetails` e i relativi due indirizzi a una tabella separata da `DetailedOrder` :</span><span class="sxs-lookup"><span data-stu-id="afdc2-176">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="afdc2-177">È anche possibile usare `TableAttribute` per eseguire questa operazione, ma si noti che questa operazione avrà esito negativo se sono presenti più spostamenti al tipo di proprietà, poiché in questo caso più tipi di entità verrebbero mappati alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="afdc2-177">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="afdc2-178">Esecuzione di query sui tipi di proprietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-178">Querying owned types</span></span>

<span data-ttu-id="afdc2-179">Quando si esegue una query sul proprietario, i tipi di proprietà saranno inclusi per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="afdc2-179">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="afdc2-180">Non è necessario utilizzare il `Include` metodo, anche se i tipi di proprietà vengono archiviati in una tabella separata.</span><span class="sxs-lookup"><span data-stu-id="afdc2-180">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="afdc2-181">In base al modello descritto in precedenza, la query seguente otterrà `Order` `OrderDetails` e le due proprietà del `StreetAddresses` database:</span><span class="sxs-lookup"><span data-stu-id="afdc2-181">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="afdc2-182">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="afdc2-182">Limitations</span></span>

<span data-ttu-id="afdc2-183">Alcune di queste limitazioni sono fondamentali per il funzionamento dei tipi di entità di proprietà, ma altre sono restrizioni che potrebbero essere rimosse nelle versioni future:</span><span class="sxs-lookup"><span data-stu-id="afdc2-183">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="afdc2-184">Limitazioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="afdc2-184">By-design restrictions</span></span>

- <span data-ttu-id="afdc2-185">Non è possibile creare un oggetto `DbSet<T>` per un tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="afdc2-185">You cannot create a `DbSet<T>` for an owned type.</span></span>
- <span data-ttu-id="afdc2-186">Non è possibile chiamare `Entity<T>()` con un tipo di proprietà in `ModelBuilder` .</span><span class="sxs-lookup"><span data-stu-id="afdc2-186">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`.</span></span>
- <span data-ttu-id="afdc2-187">Le istanze dei tipi di entità di proprietà non possono essere condivise da più proprietari (si tratta di uno scenario noto per gli oggetti valore che non possono essere implementati con i tipi di entità di proprietà).</span><span class="sxs-lookup"><span data-stu-id="afdc2-187">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types).</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="afdc2-188">Carenze correnti</span><span class="sxs-lookup"><span data-stu-id="afdc2-188">Current shortcomings</span></span>

- <span data-ttu-id="afdc2-189">I tipi di entità di proprietà non possono avere gerarchie di ereditarietà</span><span class="sxs-lookup"><span data-stu-id="afdc2-189">Owned entity types cannot have inheritance hierarchies</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="afdc2-190">Difetti nelle versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="afdc2-190">Shortcomings in previous versions</span></span>

- <span data-ttu-id="afdc2-191">In EF Core 2. x le esplorazioni dei riferimenti ai tipi di entità di proprietà non possono essere null a meno che non vengano esplicitamente mappate a una tabella separata dal proprietario.</span><span class="sxs-lookup"><span data-stu-id="afdc2-191">In EF Core 2.x reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner.</span></span>
- <span data-ttu-id="afdc2-192">In EF Core 3. x le colonne per i tipi di entità di proprietà di cui è stato eseguito il mapping alla stessa tabella del proprietario sono sempre contrassegnate come Nullable.</span><span class="sxs-lookup"><span data-stu-id="afdc2-192">In EF Core 3.x the columns for owned entity types mapped to the same table as the owner are always marked as nullable.</span></span>
