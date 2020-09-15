---
title: Proprietà Shadow-EF Core
description: Configurazione delle proprietà shadow in un modello di Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 735659a1a8523e63afa908d4fe3904e62f46cbd0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071381"
---
# <a name="shadow-properties"></a><span data-ttu-id="9f245-103">Proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="9f245-103">Shadow Properties</span></span>

<span data-ttu-id="9f245-104">Le proprietà shadow sono proprietà che non sono definite nella classe di entità .NET, ma sono definite per il tipo di entità nel modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="9f245-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="9f245-105">Il valore e lo stato di queste proprietà vengono mantenuti esclusivamente nello strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9f245-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="9f245-106">Le proprietà shadow sono utili quando nel database sono presenti dati che non devono essere esposti sui tipi di entità mappati.</span><span class="sxs-lookup"><span data-stu-id="9f245-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="9f245-107">Proprietà Shadow chiave esterna</span><span class="sxs-lookup"><span data-stu-id="9f245-107">Foreign key shadow properties</span></span>

<span data-ttu-id="9f245-108">Le proprietà shadow vengono spesso utilizzate per le proprietà di chiave esterna, in cui la relazione tra due entità è rappresentata da un valore di chiave esterna nel database, ma la relazione viene gestita sui tipi di entità utilizzando le proprietà di navigazione tra i tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="9f245-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="9f245-109">Per convenzione, EF introdurrà una proprietà shadow quando viene individuata una relazione, ma non viene trovata alcuna proprietà di chiave esterna nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="9f245-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="9f245-110">La proprietà verrà denominata `<navigation property name><principal key property name>` (la navigazione sull'entità dipendente, che fa riferimento all'entità principale, viene utilizzata per la denominazione).</span><span class="sxs-lookup"><span data-stu-id="9f245-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="9f245-111">Se il nome della proprietà chiave principale include il nome della proprietà di navigazione, il nome sarà semplicemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="9f245-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="9f245-112">Se non è presente alcuna proprietà di navigazione nell'entità dipendente, il nome del tipo di entità viene usato al suo posto.</span><span class="sxs-lookup"><span data-stu-id="9f245-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="9f245-113">Il seguente listato di codice, ad esempio, comporterà l' `BlogId` introduzione di una proprietà shadow all' `Post` entità:</span><span class="sxs-lookup"><span data-stu-id="9f245-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="9f245-114">Configurazione delle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="9f245-114">Configuring shadow properties</span></span>

<span data-ttu-id="9f245-115">Per configurare le proprietà shadow, è possibile usare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="9f245-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="9f245-116">Una volta chiamato l'overload di stringa di `Property` , è possibile concatenare qualsiasi chiamata di configurazione per altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="9f245-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="9f245-117">Nell'esempio seguente, poiché `Blog` non dispone di una proprietà CLR denominata `LastUpdated` , viene creata una proprietà shadow:</span><span class="sxs-lookup"><span data-stu-id="9f245-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="9f245-118">Se il nome fornito al `Property` metodo corrisponde al nome di una proprietà esistente (una proprietà shadow o un oggetto definito nella classe di entità), il codice configurerà la proprietà esistente anziché introdurre una nuova proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="9f245-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="9f245-119">Accesso alle proprietà shadow</span><span class="sxs-lookup"><span data-stu-id="9f245-119">Accessing shadow properties</span></span>

<span data-ttu-id="9f245-120">I valori delle proprietà shadow possono essere ottenuti e modificati tramite l' `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="9f245-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="9f245-121">È possibile fare riferimento alle proprietà shadow nelle query LINQ tramite il `EF.Property` metodo statico:</span><span class="sxs-lookup"><span data-stu-id="9f245-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="9f245-122">Non è possibile accedere alle proprietà shadow dopo una query senza rilevamento poiché le entità restituite non vengono rilevate dallo strumento di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9f245-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
