---
title: Tipi di entità-EF Core
description: Come configurare ed eseguire il mapping di tipi di entità usando Entity Framework Core
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071511"
---
# <a name="entity-types"></a><span data-ttu-id="c3804-103">Tipi di entità</span><span class="sxs-lookup"><span data-stu-id="c3804-103">Entity Types</span></span>

<span data-ttu-id="c3804-104">L'inclusione di un DbSet di un tipo nel contesto indica che è incluso nel modello di EF Core; in genere si fa riferimento a un tipo come un' *entità*.</span><span class="sxs-lookup"><span data-stu-id="c3804-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="c3804-105">EF Core possibile leggere e scrivere istanze di entità da e verso il database e, se si utilizza un database relazionale, EF Core possibile creare tabelle per le entità tramite migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c3804-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="c3804-106">Inclusione di tipi nel modello</span><span class="sxs-lookup"><span data-stu-id="c3804-106">Including types in the model</span></span>

<span data-ttu-id="c3804-107">Per convenzione, i tipi esposti nelle proprietà DbSet nel contesto sono inclusi nel modello come entità.</span><span class="sxs-lookup"><span data-stu-id="c3804-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="c3804-108">Sono inclusi anche i tipi di entità specificati nel `OnModelCreating` metodo, così come tutti i tipi individuati in modo ricorsivo per esplorare le proprietà di navigazione di altri tipi di entità individuati.</span><span class="sxs-lookup"><span data-stu-id="c3804-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="c3804-109">Nell'esempio di codice riportato di seguito vengono inclusi tutti i tipi:</span><span class="sxs-lookup"><span data-stu-id="c3804-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="c3804-110">`Blog` è incluso perché è esposto in una proprietà DbSet nel contesto.</span><span class="sxs-lookup"><span data-stu-id="c3804-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="c3804-111">`Post` è incluso perché viene individuato tramite la `Blog.Posts` proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c3804-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="c3804-112">`AuditEntry` Poiché è specificato in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="c3804-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="c3804-113">Esclusione di tipi dal modello</span><span class="sxs-lookup"><span data-stu-id="c3804-113">Excluding types from the model</span></span>

<span data-ttu-id="c3804-114">Se non si desidera che un tipo venga incluso nel modello, è possibile escluderlo:</span><span class="sxs-lookup"><span data-stu-id="c3804-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c3804-115">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="c3804-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="c3804-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c3804-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="c3804-117">Nome tabella</span><span class="sxs-lookup"><span data-stu-id="c3804-117">Table name</span></span>

<span data-ttu-id="c3804-118">Per convenzione, ogni tipo di entità verrà configurato per eseguire il mapping a una tabella di database con lo stesso nome della proprietà DbSet che espone l'entità.</span><span class="sxs-lookup"><span data-stu-id="c3804-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="c3804-119">Se non esiste alcun DbSet per l'entità specificata, viene usato il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="c3804-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="c3804-120">È possibile configurare manualmente il nome della tabella:</span><span class="sxs-lookup"><span data-stu-id="c3804-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c3804-121">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="c3804-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="c3804-122">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c3804-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="c3804-123">Schema della tabella</span><span class="sxs-lookup"><span data-stu-id="c3804-123">Table schema</span></span>

<span data-ttu-id="c3804-124">Quando si utilizza un database relazionale, le tabelle vengono create per convenzione nello schema predefinito del database.</span><span class="sxs-lookup"><span data-stu-id="c3804-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="c3804-125">Ad esempio, Microsoft SQL Server utilizzerà lo `dbo` schema (SQLite non supporta gli schemi).</span><span class="sxs-lookup"><span data-stu-id="c3804-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="c3804-126">È possibile configurare le tabelle da creare in uno schema specifico, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c3804-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c3804-127">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="c3804-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="c3804-128">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c3804-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="c3804-129">Anziché specificare lo schema per ogni tabella, è anche possibile definire lo schema predefinito a livello di modello con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="c3804-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="c3804-130">Si noti che l'impostazione dello schema predefinito influirà anche su altri oggetti di database, ad esempio le sequenze.</span><span class="sxs-lookup"><span data-stu-id="c3804-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
