---
title: Proprietà Shadow e Indexer-EF Core
description: Configurazione delle proprietà shadow e indexer in un modello di Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063894"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="10fc6-103">Proprietà Shadow e Indexer</span><span class="sxs-lookup"><span data-stu-id="10fc6-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="10fc6-104">Le proprietà shadow sono proprietà che non sono definite nella classe di entità .NET, ma sono definite per il tipo di entità nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="10fc6-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="10fc6-105">Il valore e lo stato di queste proprietà vengono mantenuti esclusivamente nello strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="10fc6-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="10fc6-106">Le proprietà shadow sono utili quando nel database sono presenti dati che non devono essere esposti sui tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="10fc6-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="10fc6-107">Proprietà Shadow chiave esterna</span><span class="sxs-lookup"><span data-stu-id="10fc6-107">Foreign key shadow properties</span></span>

<span data-ttu-id="10fc6-108">Le proprietà shadow vengono spesso utilizzate per le proprietà di chiave esterna, in cui la relazione tra due entità è rappresentata da un valore di chiave esterna nel database, ma la relazione viene gestita sui tipi di entità utilizzando le proprietà di navigazione tra i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="10fc6-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="10fc6-109">Per convenzione, EF introdurrà una proprietà shadow quando viene individuata una relazione, ma non viene trovata alcuna proprietà di chiave esterna nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="10fc6-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="10fc6-110">La proprietà verrà denominata `<navigation property name><principal key property name>` (la navigazione sull'entità dipendente, che fa riferimento all'entità principale, viene utilizzata per la denominazione).</span><span class="sxs-lookup"><span data-stu-id="10fc6-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="10fc6-111">Se il nome della proprietà chiave principale include il nome della proprietà di navigazione, il nome sarà semplicemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="10fc6-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="10fc6-112">Se non è presente alcuna proprietà di navigazione nell'entità dipendente, il nome del tipo di entità viene usato al suo posto.</span><span class="sxs-lookup"><span data-stu-id="10fc6-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="10fc6-113">Il seguente listato di codice, ad esempio, comporterà l' `BlogId` introduzione di una proprietà shadow all' `Post` entità:</span><span class="sxs-lookup"><span data-stu-id="10fc6-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="10fc6-114">Configurazione delle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="10fc6-114">Configuring shadow properties</span></span>

<span data-ttu-id="10fc6-115">Per configurare le proprietà shadow, è possibile usare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="10fc6-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="10fc6-116">Una volta chiamato l'overload di stringa di `Property` , è possibile concatenare qualsiasi chiamata di configurazione per altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="10fc6-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="10fc6-117">Nell'esempio seguente, poiché `Blog` non dispone di una proprietà CLR denominata `LastUpdated` , viene creata una proprietà shadow:</span><span class="sxs-lookup"><span data-stu-id="10fc6-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="10fc6-118">Se il nome fornito al `Property` metodo corrisponde al nome di una proprietà esistente (una proprietà shadow o un oggetto definito nella classe di entità), il codice configurerà la proprietà esistente anziché introdurre una nuova proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="10fc6-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="10fc6-119">Accesso alle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="10fc6-119">Accessing shadow properties</span></span>

<span data-ttu-id="10fc6-120">I valori delle proprietà shadow possono essere ottenuti e modificati tramite l' `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="10fc6-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="10fc6-121">È possibile fare riferimento alle proprietà shadow nelle query LINQ tramite il `EF.Property` metodo statico:</span><span class="sxs-lookup"><span data-stu-id="10fc6-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="10fc6-122">Non è possibile accedere alle proprietà shadow dopo una query senza rilevamento poiché le entità restituite non vengono rilevate dallo strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="10fc6-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="10fc6-123">Tipi di entità contenitore delle proprietà</span><span class="sxs-lookup"><span data-stu-id="10fc6-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="10fc6-124">Il supporto per i tipi di entità contenitore delle proprietà è stato aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="10fc6-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="10fc6-125">I tipi di entità che contengono solo proprietà dell'indicizzatore sono noti come tipi di entità del contenitore delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="10fc6-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="10fc6-126">Questi tipi di entità non dispongono di proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="10fc6-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="10fc6-127">Attualmente `Dictionary<string, object>` è supportato solo come tipo di entità contenitore delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="10fc6-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="10fc6-128">Questo significa che deve essere configurato come tipo di entità condiviso con un nome univoco e la proprietà corrispondente `DbSet` deve essere implementata tramite una `Set` chiamata a.</span><span class="sxs-lookup"><span data-stu-id="10fc6-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]