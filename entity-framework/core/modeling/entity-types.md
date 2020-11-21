---
title: Tipi di entità-EF Core
description: Come configurare ed eseguire il mapping di tipi di entità usando Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 9d86b959b5e0360df6d782d8d1c1c2f9393fdf8b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003497"
---
# <a name="entity-types"></a><span data-ttu-id="d624c-103">Tipi di entità</span><span class="sxs-lookup"><span data-stu-id="d624c-103">Entity Types</span></span>

<span data-ttu-id="d624c-104">L'inclusione di un DbSet di un tipo nel contesto indica che è incluso nel modello di EF Core; in genere si fa riferimento a un tipo come un' *entità*.</span><span class="sxs-lookup"><span data-stu-id="d624c-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="d624c-105">EF Core possibile leggere e scrivere istanze di entità da e verso il database e, se si utilizza un database relazionale, EF Core possibile creare tabelle per le entità tramite migrazioni.</span><span class="sxs-lookup"><span data-stu-id="d624c-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="d624c-106">Inclusione di tipi nel modello</span><span class="sxs-lookup"><span data-stu-id="d624c-106">Including types in the model</span></span>

<span data-ttu-id="d624c-107">Per convenzione, i tipi esposti nelle proprietà DbSet nel contesto sono inclusi nel modello come entità.</span><span class="sxs-lookup"><span data-stu-id="d624c-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="d624c-108">Sono inclusi anche i tipi di entità specificati nel `OnModelCreating` metodo, così come tutti i tipi individuati in modo ricorsivo per esplorare le proprietà di navigazione di altri tipi di entità individuati.</span><span class="sxs-lookup"><span data-stu-id="d624c-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="d624c-109">Nell'esempio di codice riportato di seguito vengono inclusi tutti i tipi:</span><span class="sxs-lookup"><span data-stu-id="d624c-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="d624c-110">`Blog` è incluso perché è esposto in una proprietà DbSet nel contesto.</span><span class="sxs-lookup"><span data-stu-id="d624c-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="d624c-111">`Post` è incluso perché viene individuato tramite la `Blog.Posts` proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="d624c-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="d624c-112">`AuditEntry` Poiché è specificato in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="d624c-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="d624c-113">Esclusione di tipi dal modello</span><span class="sxs-lookup"><span data-stu-id="d624c-113">Excluding types from the model</span></span>

<span data-ttu-id="d624c-114">Se non si desidera che un tipo venga incluso nel modello, è possibile escluderlo:</span><span class="sxs-lookup"><span data-stu-id="d624c-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d624c-115">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="d624c-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="d624c-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d624c-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="d624c-117">Esclusione dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="d624c-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="d624c-118">La possibilità di escludere tabelle dalle migrazioni è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="d624c-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="d624c-119">A volte è utile avere lo stesso tipo di entità mappato in più `DbContext` tipi.</span><span class="sxs-lookup"><span data-stu-id="d624c-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="d624c-120">Ciò vale soprattutto quando si usano i [contesti delimitati](https://www.martinfowler.com/bliki/BoundedContext.html), per i quali è comune avere un `DbContext` tipo diverso per ogni contesto delimitato.</span><span class="sxs-lookup"><span data-stu-id="d624c-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="d624c-121">Con queste migrazioni di configurazione la tabella non viene creata `AspNetUsers` , ma `IdentityUser` è ancora inclusa nel modello e può essere utilizzata normalmente.</span><span class="sxs-lookup"><span data-stu-id="d624c-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="d624c-122">Se è necessario iniziare a gestire la tabella usando di nuovo le migrazioni, è necessario creare una nuova migrazione in cui `AspNetUsers` non è escluso.</span><span class="sxs-lookup"><span data-stu-id="d624c-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="d624c-123">La prossima migrazione conterrà ora le modifiche apportate alla tabella.</span><span class="sxs-lookup"><span data-stu-id="d624c-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="d624c-124">Nome tabella</span><span class="sxs-lookup"><span data-stu-id="d624c-124">Table name</span></span>

<span data-ttu-id="d624c-125">Per convenzione, ogni tipo di entità verrà configurato per eseguire il mapping a una tabella di database con lo stesso nome della proprietà DbSet che espone l'entità.</span><span class="sxs-lookup"><span data-stu-id="d624c-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="d624c-126">Se non esiste alcun DbSet per l'entità specificata, viene usato il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="d624c-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="d624c-127">È possibile configurare manualmente il nome della tabella:</span><span class="sxs-lookup"><span data-stu-id="d624c-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d624c-128">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="d624c-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="d624c-129">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d624c-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

<span data-ttu-id="d624c-130">\*\*_</span><span class="sxs-lookup"><span data-stu-id="d624c-130">\*\*_</span></span>

## <a name="table-schema"></a><span data-ttu-id="d624c-131">Schema della tabella</span><span class="sxs-lookup"><span data-stu-id="d624c-131">Table schema</span></span>

<span data-ttu-id="d624c-132">Quando si utilizza un database relazionale, le tabelle vengono create per convenzione nello schema predefinito del database.</span><span class="sxs-lookup"><span data-stu-id="d624c-132">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="d624c-133">Ad esempio, Microsoft SQL Server utilizzerà lo `dbo` schema (SQLite non supporta gli schemi).</span><span class="sxs-lookup"><span data-stu-id="d624c-133">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="d624c-134">È possibile configurare le tabelle da creare in uno schema specifico, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="d624c-134">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d624c-135">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="d624c-135">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="d624c-136">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d624c-136">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

<span data-ttu-id="d624c-137">_\*\*</span><span class="sxs-lookup"><span data-stu-id="d624c-137">_\*\*</span></span>

<span data-ttu-id="d624c-138">Anziché specificare lo schema per ogni tabella, è anche possibile definire lo schema predefinito a livello di modello con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="d624c-138">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="d624c-139">Si noti che l'impostazione dello schema predefinito influirà anche su altri oggetti di database, ad esempio le sequenze.</span><span class="sxs-lookup"><span data-stu-id="d624c-139">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="d624c-140">Visualizza mapping</span><span class="sxs-lookup"><span data-stu-id="d624c-140">View mapping</span></span>

<span data-ttu-id="d624c-141">È possibile eseguire il mapping dei tipi di entità alle viste di database usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="d624c-141">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="d624c-142">EF presuppone che la vista a cui si fa riferimento sia già presente nel database, non la creerà automaticamente in una migrazione.</span><span class="sxs-lookup"><span data-stu-id="d624c-142">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="d624c-143">Il mapping a una vista consente di rimuovere il mapping predefinito della tabella, ma a partire da EF 5,0 il tipo di entità può anche essere mappato in modo esplicito a una tabella.</span><span class="sxs-lookup"><span data-stu-id="d624c-143">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="d624c-144">In questo caso, il mapping delle query verrà usato per le query e il mapping delle tabelle verrà usato per gli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="d624c-144">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="d624c-145">Per testare i tipi di entità di cui è stato eseguito il mapping alle viste usando il provider in memoria, eseguirne il mapping a una query tramite `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="d624c-145">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="d624c-146">Per altri dettagli, vedere un [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) usando questa tecnica.</span><span class="sxs-lookup"><span data-stu-id="d624c-146">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
