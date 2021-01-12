---
title: Indici-EF Core
description: Configurazione di indici in un modello di Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128537"
---
# <a name="indexes"></a><span data-ttu-id="66e21-103">Indici</span><span class="sxs-lookup"><span data-stu-id="66e21-103">Indexes</span></span>

<span data-ttu-id="66e21-104">Gli indici sono un concetto comune in molti archivi dati.</span><span class="sxs-lookup"><span data-stu-id="66e21-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="66e21-105">Mentre la loro implementazione nell'archivio dati può variare, vengono usati per rendere più efficienti le ricerche basate su una colonna o un set di colonne.</span><span class="sxs-lookup"><span data-stu-id="66e21-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span> <span data-ttu-id="66e21-106">Per ulteriori informazioni sull'utilizzo di un indice efficace, vedere la [sezione indici](xref:core/performance/efficient-querying#use-indexes-properly) nella documentazione relativa alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="66e21-106">See the [indexes section](xref:core/performance/efficient-querying#use-indexes-properly) in the performance documentation for more information on good index usage.</span></span>

<span data-ttu-id="66e21-107">È possibile specificare un indice su una colonna come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="66e21-107">You can specify an index over a column as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="66e21-108">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="66e21-108">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> <span data-ttu-id="66e21-109">La configurazione degli indici tramite le annotazioni dei dati è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="66e21-109">Configuring indexes via Data Annotations has been introduced in EF Core 5.0.</span></span>

## <a name="fluent-api"></a>[<span data-ttu-id="66e21-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="66e21-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> <span data-ttu-id="66e21-111">Per convenzione, viene creato un indice in ogni proprietà o set di proprietà utilizzate come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="66e21-111">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="66e21-112">EF Core supporta solo un indice per set di proprietà distinti.</span><span class="sxs-lookup"><span data-stu-id="66e21-112">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="66e21-113">Se si configura un indice in un set di proprietà in cui è già definito un indice, per convenzione o per una configurazione precedente, si modificherà la definizione di tale indice.</span><span class="sxs-lookup"><span data-stu-id="66e21-113">If you configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="66e21-114">Questa opzione è utile se si desidera configurare ulteriormente un indice creato per convenzione.</span><span class="sxs-lookup"><span data-stu-id="66e21-114">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="composite-index"></a><span data-ttu-id="66e21-115">Indice composto</span><span class="sxs-lookup"><span data-stu-id="66e21-115">Composite index</span></span>

<span data-ttu-id="66e21-116">Un indice può inoltre estendersi su più di una colonna:</span><span class="sxs-lookup"><span data-stu-id="66e21-116">An index can also span more than one column:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66e21-117">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="66e21-117">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66e21-118">API Fluent</span><span class="sxs-lookup"><span data-stu-id="66e21-118">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

<span data-ttu-id="66e21-119">\*\*_</span><span class="sxs-lookup"><span data-stu-id="66e21-119">\*\*_</span></span>

<span data-ttu-id="66e21-120">Gli indici su più colonne, noti anche come indici _composite \*, velocizzano le query che filtrano le colonne dell'indice, ma anche le query che filtrano solo le *prime* colonne coperte dall'indice.</span><span class="sxs-lookup"><span data-stu-id="66e21-120">Indexes over multiple columns, also known as _composite indexes\*, speed up queries which filter on index's columns, but also queries which only filter on the *first* columns covered by the index.</span></span> <span data-ttu-id="66e21-121">Per ulteriori informazioni, vedere la documentazione relativa alle [prestazioni](xref:core/performance/efficient-querying#use-indexes-properly) .</span><span class="sxs-lookup"><span data-stu-id="66e21-121">See the [performance docs](xref:core/performance/efficient-querying#use-indexes-properly) for more information.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="66e21-122">Univocità indice</span><span class="sxs-lookup"><span data-stu-id="66e21-122">Index uniqueness</span></span>

<span data-ttu-id="66e21-123">Per impostazione predefinita, gli indici non sono univoci: più righe possono avere gli stessi valori per il set di colonne dell'indice.</span><span class="sxs-lookup"><span data-stu-id="66e21-123">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="66e21-124">È possibile rendere univoco un indice nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="66e21-124">You can make an index unique as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66e21-125">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="66e21-125">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66e21-126">API Fluent</span><span class="sxs-lookup"><span data-stu-id="66e21-126">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

<span data-ttu-id="66e21-127">Se si tenta di inserire più di un'entità con gli stessi valori per il set di colonne dell'indice, verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="66e21-127">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="66e21-128">Nome dell'indice</span><span class="sxs-lookup"><span data-stu-id="66e21-128">Index name</span></span>

<span data-ttu-id="66e21-129">Per convenzione, gli indici creati in un database relazionale vengono denominati `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="66e21-129">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="66e21-130">Per gli indici composti, `<property name>` diventa un elenco di nomi di proprietà separato da un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="66e21-130">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="66e21-131">È possibile impostare il nome dell'indice creato nel database:</span><span class="sxs-lookup"><span data-stu-id="66e21-131">You can set the name of the index created in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66e21-132">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="66e21-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66e21-133">API Fluent</span><span class="sxs-lookup"><span data-stu-id="66e21-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a><span data-ttu-id="66e21-134">Filtro indice</span><span class="sxs-lookup"><span data-stu-id="66e21-134">Index filter</span></span>

<span data-ttu-id="66e21-135">Alcuni database relazionali consentono di specificare un indice filtrato o parziale.</span><span class="sxs-lookup"><span data-stu-id="66e21-135">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="66e21-136">Ciò consente di indicizzare solo un subset di valori di una colonna, riducendo le dimensioni dell'indice e migliorando le prestazioni e l'utilizzo dello spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="66e21-136">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="66e21-137">Per ulteriori informazioni su SQL Server indici filtrati, [vedere la documentazione](/sql/relational-databases/indexes/create-filtered-indexes)di.</span><span class="sxs-lookup"><span data-stu-id="66e21-137">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="66e21-138">È possibile utilizzare l'API Fluent per specificare un filtro per un indice, fornito come espressione SQL:</span><span class="sxs-lookup"><span data-stu-id="66e21-138">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="66e21-139">Quando si usa il provider di SQL Server EF aggiunge un `'IS NOT NULL'` filtro per tutte le colonne nullable che fanno parte di un indice univoco.</span><span class="sxs-lookup"><span data-stu-id="66e21-139">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="66e21-140">Per eseguire l'override di questa convenzione, è possibile specificare un `null` valore.</span><span class="sxs-lookup"><span data-stu-id="66e21-140">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="66e21-141">included_columns</span><span class="sxs-lookup"><span data-stu-id="66e21-141">Included columns</span></span>

<span data-ttu-id="66e21-142">Alcuni database relazionali consentono di configurare un set di colonne che vengono incluse nell'indice, ma non fanno parte della relativa chiave.</span><span class="sxs-lookup"><span data-stu-id="66e21-142">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="66e21-143">Questo può migliorare significativamente le prestazioni di esecuzione delle query quando tutte le colonne della query sono incluse nell'indice come colonne chiave o colonne, in quanto non è necessario accedere alla tabella stessa.</span><span class="sxs-lookup"><span data-stu-id="66e21-143">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="66e21-144">Per ulteriori informazioni su SQL Server colonne incluse, [vedere la documentazione](/sql/relational-databases/indexes/create-indexes-with-included-columns)di.</span><span class="sxs-lookup"><span data-stu-id="66e21-144">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="66e21-145">Nell'esempio seguente la `Url` colonna fa parte della chiave di indice, pertanto qualsiasi filtro di query su tale colonna può usare l'indice.</span><span class="sxs-lookup"><span data-stu-id="66e21-145">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="66e21-146">Inoltre, le query che accedono solo alle `Title` `PublishedOn` colonne e non dovranno accedere alla tabella e vengono eseguite in modo più efficiente:</span><span class="sxs-lookup"><span data-stu-id="66e21-146">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
