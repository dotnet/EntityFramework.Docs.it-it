---
title: Proprietà Shadow-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b24ff85d8f5910a5625910e7225a94112d769824
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238281"
---
# <a name="shadow-properties"></a><span data-ttu-id="cd3b6-102">Proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="cd3b6-102">Shadow Properties</span></span>

<span data-ttu-id="cd3b6-103">Le proprietà shadow sono proprietà che non sono definite nella classe di entità .NET, ma sono definite per il tipo di entità nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="cd3b6-104">Il valore e lo stato di queste proprietà vengono mantenuti esclusivamente nello strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="cd3b6-105">Le proprietà shadow sono utili quando nel database sono presenti dati che non devono essere esposti sui tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="cd3b6-106">Proprietà Shadow chiave esterna</span><span class="sxs-lookup"><span data-stu-id="cd3b6-106">Foreign key shadow properties</span></span>

<span data-ttu-id="cd3b6-107">Le proprietà shadow vengono spesso utilizzate per le proprietà di chiave esterna, in cui la relazione tra due entità è rappresentata da un valore di chiave esterna nel database, ma la relazione viene gestita sui tipi di entità utilizzando le proprietà di navigazione tra i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-107">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="cd3b6-108">Per convenzione, EF introdurrà una proprietà shadow quando viene individuata una relazione, ma non viene trovata alcuna proprietà di chiave esterna nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-108">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="cd3b6-109">La proprietà verrà denominata `<navigation property name><principal key property name>` (la navigazione sull'entità dipendente, che fa riferimento all'entità principale, viene utilizzata per la denominazione).</span><span class="sxs-lookup"><span data-stu-id="cd3b6-109">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="cd3b6-110">Se il nome della proprietà chiave principale include il nome della proprietà di navigazione, il nome sarà semplicemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="cd3b6-110">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="cd3b6-111">Se non è presente alcuna proprietà di navigazione nell'entità dipendente, il nome del tipo di entità viene usato al suo posto.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-111">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="cd3b6-112">Il seguente listato di codice, ad esempio, comporterà l' `BlogId` introduzione di una proprietà shadow all' `Post` entità:</span><span class="sxs-lookup"><span data-stu-id="cd3b6-112">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="cd3b6-113">Configurazione delle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="cd3b6-113">Configuring shadow properties</span></span>

<span data-ttu-id="cd3b6-114">Per configurare le proprietà shadow, è possibile usare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-114">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="cd3b6-115">Una volta chiamato l'overload di stringa di `Property` , è possibile concatenare qualsiasi chiamata di configurazione per altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-115">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="cd3b6-116">Nell'esempio seguente, poiché `Blog` non dispone di una proprietà CLR denominata `LastUpdated` , viene creata una proprietà shadow:</span><span class="sxs-lookup"><span data-stu-id="cd3b6-116">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="cd3b6-117">Se il nome fornito al `Property` metodo corrisponde al nome di una proprietà esistente (una proprietà shadow o un oggetto definito nella classe di entità), il codice configurerà la proprietà esistente anziché introdurre una nuova proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-117">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="cd3b6-118">Accesso alle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="cd3b6-118">Accessing shadow properties</span></span>

<span data-ttu-id="cd3b6-119">I valori delle proprietà shadow possono essere ottenuti e modificati tramite l' `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="cd3b6-119">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="cd3b6-120">È possibile fare riferimento alle proprietà shadow nelle query LINQ tramite il `EF.Property` metodo statico:</span><span class="sxs-lookup"><span data-stu-id="cd3b6-120">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="cd3b6-121">Non è possibile accedere alle proprietà shadow dopo una query senza rilevamento poiché le entità restituite non vengono rilevate dallo strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cd3b6-121">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
