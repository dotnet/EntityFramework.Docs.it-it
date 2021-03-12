---
title: Tipi di entità-EF Core
description: Come configurare ed eseguire il mapping di tipi di entità usando Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 6c8029b64210dd9bc69fd53d9a3b4aa06bf519e0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023991"
---
# <a name="entity-types"></a><span data-ttu-id="b02e7-103">Tipi di entità</span><span class="sxs-lookup"><span data-stu-id="b02e7-103">Entity Types</span></span>

<span data-ttu-id="b02e7-104">L'inclusione di un DbSet di un tipo nel contesto indica che è incluso nel modello di EF Core; in genere si fa riferimento a un tipo come un' *entità*.</span><span class="sxs-lookup"><span data-stu-id="b02e7-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="b02e7-105">EF Core possibile leggere e scrivere istanze di entità da e verso il database e, se si utilizza un database relazionale, EF Core possibile creare tabelle per le entità tramite migrazioni.</span><span class="sxs-lookup"><span data-stu-id="b02e7-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="b02e7-106">Inclusione di tipi nel modello</span><span class="sxs-lookup"><span data-stu-id="b02e7-106">Including types in the model</span></span>

<span data-ttu-id="b02e7-107">Per convenzione, i tipi esposti nelle proprietà DbSet nel contesto sono inclusi nel modello come entità.</span><span class="sxs-lookup"><span data-stu-id="b02e7-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="b02e7-108">Sono inclusi anche i tipi di entità specificati nel `OnModelCreating` metodo, così come tutti i tipi individuati in modo ricorsivo per esplorare le proprietà di navigazione di altri tipi di entità individuati.</span><span class="sxs-lookup"><span data-stu-id="b02e7-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="b02e7-109">Nell'esempio di codice riportato di seguito vengono inclusi tutti i tipi:</span><span class="sxs-lookup"><span data-stu-id="b02e7-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="b02e7-110">`Blog` è incluso perché è esposto in una proprietà DbSet nel contesto.</span><span class="sxs-lookup"><span data-stu-id="b02e7-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="b02e7-111">`Post` è incluso perché viene individuato tramite la `Blog.Posts` proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="b02e7-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="b02e7-112">`AuditEntry` Poiché è specificato in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="b02e7-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="b02e7-113">Esclusione di tipi dal modello</span><span class="sxs-lookup"><span data-stu-id="b02e7-113">Excluding types from the model</span></span>

<span data-ttu-id="b02e7-114">Se non si desidera che un tipo venga incluso nel modello, è possibile escluderlo:</span><span class="sxs-lookup"><span data-stu-id="b02e7-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b02e7-115">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="b02e7-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="b02e7-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="b02e7-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="b02e7-117">Esclusione dalle migrazioni</span><span class="sxs-lookup"><span data-stu-id="b02e7-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="b02e7-118">La possibilità di escludere tabelle dalle migrazioni è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="b02e7-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="b02e7-119">A volte è utile avere lo stesso tipo di entità mappato in più `DbContext` tipi.</span><span class="sxs-lookup"><span data-stu-id="b02e7-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="b02e7-120">Ciò vale soprattutto quando si usano i [contesti delimitati](https://www.martinfowler.com/bliki/BoundedContext.html), per i quali è comune avere un `DbContext` tipo diverso per ogni contesto delimitato.</span><span class="sxs-lookup"><span data-stu-id="b02e7-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="b02e7-121">Con queste migrazioni di configurazione la tabella non viene creata `AspNetUsers` , ma `IdentityUser` è ancora inclusa nel modello e può essere utilizzata normalmente.</span><span class="sxs-lookup"><span data-stu-id="b02e7-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="b02e7-122">Se è necessario iniziare a gestire la tabella usando di nuovo le migrazioni, è necessario creare una nuova migrazione in cui `AspNetUsers` non è escluso.</span><span class="sxs-lookup"><span data-stu-id="b02e7-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="b02e7-123">La prossima migrazione conterrà ora le modifiche apportate alla tabella.</span><span class="sxs-lookup"><span data-stu-id="b02e7-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="b02e7-124">Nome tabella</span><span class="sxs-lookup"><span data-stu-id="b02e7-124">Table name</span></span>

<span data-ttu-id="b02e7-125">Per convenzione, ogni tipo di entità verrà configurato per eseguire il mapping a una tabella di database con lo stesso nome della proprietà DbSet che espone l'entità.</span><span class="sxs-lookup"><span data-stu-id="b02e7-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="b02e7-126">Se non esiste alcun DbSet per l'entità specificata, viene usato il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="b02e7-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="b02e7-127">È possibile configurare manualmente il nome della tabella:</span><span class="sxs-lookup"><span data-stu-id="b02e7-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b02e7-128">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="b02e7-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="b02e7-129">API Fluent</span><span class="sxs-lookup"><span data-stu-id="b02e7-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="b02e7-130">Schema della tabella</span><span class="sxs-lookup"><span data-stu-id="b02e7-130">Table schema</span></span>

<span data-ttu-id="b02e7-131">Quando si utilizza un database relazionale, le tabelle vengono create per convenzione nello schema predefinito del database.</span><span class="sxs-lookup"><span data-stu-id="b02e7-131">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="b02e7-132">Ad esempio, Microsoft SQL Server utilizzerà lo `dbo` schema (SQLite non supporta gli schemi).</span><span class="sxs-lookup"><span data-stu-id="b02e7-132">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="b02e7-133">È possibile configurare le tabelle da creare in uno schema specifico, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="b02e7-133">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b02e7-134">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="b02e7-134">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="b02e7-135">API Fluent</span><span class="sxs-lookup"><span data-stu-id="b02e7-135">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="b02e7-136">Anziché specificare lo schema per ogni tabella, è anche possibile definire lo schema predefinito a livello di modello con l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="b02e7-136">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="b02e7-137">Si noti che l'impostazione dello schema predefinito influirà anche su altri oggetti di database, ad esempio le sequenze.</span><span class="sxs-lookup"><span data-stu-id="b02e7-137">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="b02e7-138">Visualizza mapping</span><span class="sxs-lookup"><span data-stu-id="b02e7-138">View mapping</span></span>

<span data-ttu-id="b02e7-139">È possibile eseguire il mapping dei tipi di entità alle viste di database usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="b02e7-139">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="b02e7-140">EF presuppone che la vista a cui si fa riferimento sia già presente nel database, non la creerà automaticamente in una migrazione.</span><span class="sxs-lookup"><span data-stu-id="b02e7-140">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="b02e7-141">Il mapping a una vista consente di rimuovere il mapping predefinito della tabella, ma a partire da EF 5,0 il tipo di entità può anche essere mappato in modo esplicito a una tabella.</span><span class="sxs-lookup"><span data-stu-id="b02e7-141">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="b02e7-142">In questo caso, il mapping delle query verrà usato per le query e il mapping delle tabelle verrà usato per gli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="b02e7-142">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="b02e7-143">Per testare i tipi di entità di cui è stato eseguito il mapping alle viste usando il provider in memoria, eseguirne il mapping a una query tramite `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="b02e7-143">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="b02e7-144">Per altri dettagli, vedere un [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) usando questa tecnica.</span><span class="sxs-lookup"><span data-stu-id="b02e7-144">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>

## <a name="table-valued-function-mapping"></a><span data-ttu-id="b02e7-145">Mapping di funzioni con valori di tabella</span><span class="sxs-lookup"><span data-stu-id="b02e7-145">Table-valued function mapping</span></span>

<span data-ttu-id="b02e7-146">È possibile eseguire il mapping di un tipo di entità a una funzione con valori di tabella (TVF) anziché a una tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="b02e7-146">It's possible to map an entity type to a table-valued function (TVF) instead of a table in the database.</span></span> <span data-ttu-id="b02e7-147">Per illustrare questo problema, è possibile definire un'altra entità che rappresenta il Blog con più post.</span><span class="sxs-lookup"><span data-stu-id="b02e7-147">To illustrate this, let's define another entity that represents blog with multiple posts.</span></span> <span data-ttu-id="b02e7-148">Nell'esempio, l'entità è senza [chiave](xref:core/modeling/keyless-entity-types), ma non deve essere.</span><span class="sxs-lookup"><span data-stu-id="b02e7-148">In the example, the entity is [keyless](xref:core/modeling/keyless-entity-types), but it doesn't have to be.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

<span data-ttu-id="b02e7-149">Successivamente, creare la funzione con valori di tabella seguente nel database, che restituisce solo i Blog con più post, oltre al numero di post associati a ognuno di questi Blog:</span><span class="sxs-lookup"><span data-stu-id="b02e7-149">Next, create the following table-valued function in the database, which returns only blogs with multiple posts as well as the number of posts associated with each of these blogs:</span></span>

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

<span data-ttu-id="b02e7-150">A questo punto, `BlogWithMultiplePost` è possibile eseguire il mapping dell'entità a questa funzione nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="b02e7-150">Now, the entity `BlogWithMultiplePost` can be mapped to this function in a following way:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> <span data-ttu-id="b02e7-151">Per eseguire il mapping di un'entità a una funzione con valori di tabella, la funzione deve essere senza parametri.</span><span class="sxs-lookup"><span data-stu-id="b02e7-151">In order to map an entity to a table-valued function the function must be parameterless.</span></span>

<span data-ttu-id="b02e7-152">Convenzionalmente, le proprietà dell'entità verranno mappate alle colonne corrispondenti restituite da TVF.</span><span class="sxs-lookup"><span data-stu-id="b02e7-152">Conventionally the entity properties will be mapped to matching columns returned by the TVF.</span></span> <span data-ttu-id="b02e7-153">Se le colonne restituite da TVF hanno un nome diverso rispetto alla proprietà dell'entità, è possibile configurarle usando il `HasColumnName` metodo, come nel caso del mapping a una tabella normale.</span><span class="sxs-lookup"><span data-stu-id="b02e7-153">If the columns returned by TVF has different name than entity property then it can be configured using `HasColumnName` method, just like when mapping to a regular table.</span></span>

<span data-ttu-id="b02e7-154">Quando il tipo di entità viene mappato a una funzione con valori di tabella, la query:</span><span class="sxs-lookup"><span data-stu-id="b02e7-154">When the entity type is mapped to a table-valued function, the query:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

<span data-ttu-id="b02e7-155">Produce il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="b02e7-155">Produces the following SQL:</span></span>

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a><span data-ttu-id="b02e7-156">Commenti tabella</span><span class="sxs-lookup"><span data-stu-id="b02e7-156">Table comments</span></span>

<span data-ttu-id="b02e7-157">È possibile impostare un commento di testo arbitrario che viene impostato nella tabella di database, consentendo di documentare lo schema nel database:</span><span class="sxs-lookup"><span data-stu-id="b02e7-157">You can set an arbitrary text comment that gets set on the database table, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b02e7-158">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="b02e7-158">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="b02e7-159">L'impostazione di commenti tramite annotazioni dati è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="b02e7-159">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="b02e7-160">API Fluent</span><span class="sxs-lookup"><span data-stu-id="b02e7-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

***
