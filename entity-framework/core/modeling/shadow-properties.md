---
title: Proprietà Shadow e Indexer-EF Core
description: Configurazione delle proprietà shadow e indexer in un modello di Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: d419de2da2a9fc29e675dde76e824217347d2e9c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003432"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="81212-103">Proprietà Shadow e Indexer</span><span class="sxs-lookup"><span data-stu-id="81212-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="81212-104">Le proprietà shadow sono proprietà che non sono definite nella classe di entità .NET, ma sono definite per il tipo di entità nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="81212-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="81212-105">Il valore e lo stato di queste proprietà vengono mantenuti esclusivamente nello strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="81212-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="81212-106">Le proprietà shadow sono utili quando nel database sono presenti dati che non devono essere esposti sui tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="81212-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="81212-107">Le proprietà dell'indicizzatore sono proprietà del tipo di entità, supportate da un [indicizzatore](/dotnet/csharp/programming-guide/indexers/) nella classe di entità .NET.</span><span class="sxs-lookup"><span data-stu-id="81212-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="81212-108">È possibile accedervi usando l'indicizzatore nelle istanze della classe .NET.</span><span class="sxs-lookup"><span data-stu-id="81212-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="81212-109">Consente inoltre di aggiungere ulteriori proprietà al tipo di entità senza modificare la classe CLR.</span><span class="sxs-lookup"><span data-stu-id="81212-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="81212-110">Proprietà Shadow chiave esterna</span><span class="sxs-lookup"><span data-stu-id="81212-110">Foreign key shadow properties</span></span>

<span data-ttu-id="81212-111">Le proprietà shadow vengono spesso utilizzate per le proprietà di chiave esterna, in cui la relazione tra due entità è rappresentata da un valore di chiave esterna nel database, ma la relazione viene gestita sui tipi di entità utilizzando le proprietà di navigazione tra i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="81212-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="81212-112">Per convenzione, EF introdurrà una proprietà shadow quando viene individuata una relazione, ma non viene trovata alcuna proprietà di chiave esterna nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="81212-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="81212-113">La proprietà verrà denominata `<navigation property name><principal key property name>` (la navigazione sull'entità dipendente, che fa riferimento all'entità principale, viene utilizzata per la denominazione).</span><span class="sxs-lookup"><span data-stu-id="81212-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="81212-114">Se il nome della proprietà chiave principale include il nome della proprietà di navigazione, il nome sarà semplicemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="81212-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="81212-115">Se non è presente alcuna proprietà di navigazione nell'entità dipendente, il nome del tipo di entità viene usato al suo posto.</span><span class="sxs-lookup"><span data-stu-id="81212-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="81212-116">Il seguente listato di codice, ad esempio, comporterà l' `BlogId` introduzione di una proprietà shadow all' `Post` entità:</span><span class="sxs-lookup"><span data-stu-id="81212-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="81212-117">Configurazione delle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="81212-117">Configuring shadow properties</span></span>

<span data-ttu-id="81212-118">Per configurare le proprietà shadow, è possibile usare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="81212-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="81212-119">Una volta chiamato l'overload di stringa di `Property` , è possibile concatenare qualsiasi chiamata di configurazione per altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="81212-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="81212-120">Nell'esempio seguente, poiché `Blog` non dispone di una proprietà CLR denominata `LastUpdated` , viene creata una proprietà shadow:</span><span class="sxs-lookup"><span data-stu-id="81212-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="81212-121">Se il nome fornito al `Property` metodo corrisponde al nome di una proprietà esistente (una proprietà shadow o un oggetto definito nella classe di entità), il codice configurerà la proprietà esistente anziché introdurre una nuova proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="81212-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="81212-122">Accesso alle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="81212-122">Accessing shadow properties</span></span>

<span data-ttu-id="81212-123">I valori delle proprietà shadow possono essere ottenuti e modificati tramite l' `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="81212-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="81212-124">È possibile fare riferimento alle proprietà shadow nelle query LINQ tramite il `EF.Property` metodo statico:</span><span class="sxs-lookup"><span data-stu-id="81212-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="81212-125">Non è possibile accedere alle proprietà shadow dopo una query senza rilevamento poiché le entità restituite non vengono rilevate dallo strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="81212-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="81212-126">Configurazione delle proprietà dell'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="81212-126">Configuring indexer properties</span></span>

<span data-ttu-id="81212-127">È possibile usare l'API Fluent per configurare le proprietà dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="81212-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="81212-128">Una volta chiamato il metodo `IndexerProperty` , è possibile concatenare qualsiasi chiamata di configurazione per le altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="81212-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="81212-129">Nell'esempio seguente viene `Blog` definito un indicizzatore che verrà usato per creare una proprietà dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="81212-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="81212-130">Se il nome fornito al `IndexerProperty` metodo corrisponde al nome di una proprietà dell'indicizzatore esistente, il codice configurerà la proprietà esistente.</span><span class="sxs-lookup"><span data-stu-id="81212-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="81212-131">Se il tipo di entità dispone di una proprietà, supportata da una proprietà nella classe di entità, viene generata un'eccezione perché le proprietà dell'indicizzatore devono essere accessibili solo tramite l'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="81212-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="81212-132">Tipi di entità contenitore delle proprietà</span><span class="sxs-lookup"><span data-stu-id="81212-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="81212-133">Il supporto per i tipi di entità contenitore delle proprietà è stato introdotto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="81212-133">Support for Property bag entity types was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="81212-134">I tipi di entità che contengono solo proprietà dell'indicizzatore sono noti come tipi di entità del contenitore delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="81212-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="81212-135">Questi tipi di entità non hanno proprietà shadow, ma EF creerà le proprietà dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="81212-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="81212-136">Attualmente `Dictionary<string, object>` è supportato solo come tipo di entità contenitore delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="81212-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="81212-137">Deve essere configurata come tipo di entità condivisa con un nome univoco e la `DbSet` proprietà corrispondente deve essere implementata tramite una `Set` chiamata a.</span><span class="sxs-lookup"><span data-stu-id="81212-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
