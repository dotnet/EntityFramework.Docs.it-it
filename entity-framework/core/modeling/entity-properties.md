---
title: Proprietà entità-EF Core
description: Come configurare e mappare le proprietà di un'entità usando Entity Framework Core
author: lajones
ms.date: 05/27/2020
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: d4e4c50d8c7febf5e42e9aa39352c0bb6a6bd409
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238216"
---
# <a name="entity-properties"></a><span data-ttu-id="fec9e-103">Proprietà delle entità</span><span class="sxs-lookup"><span data-stu-id="fec9e-103">Entity Properties</span></span>

<span data-ttu-id="fec9e-104">Ogni tipo di entità nel modello dispone di un set di proprietà, che EF Core leggerà e scriverà dal database.</span><span class="sxs-lookup"><span data-stu-id="fec9e-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="fec9e-105">Se si usa un database relazionale, le proprietà dell'entità vengono mappate alle colonne della tabella.</span><span class="sxs-lookup"><span data-stu-id="fec9e-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="fec9e-106">Proprietà incluse ed escluse</span><span class="sxs-lookup"><span data-stu-id="fec9e-106">Included and excluded properties</span></span>

<span data-ttu-id="fec9e-107">Per convenzione, tutte le proprietà pubbliche con un getter e un setter verranno incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="fec9e-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="fec9e-108">Le proprietà specifiche possono essere escluse come segue:</span><span class="sxs-lookup"><span data-stu-id="fec9e-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-109">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="fec9e-111">Nome colonna</span><span class="sxs-lookup"><span data-stu-id="fec9e-111">Column names</span></span>

<span data-ttu-id="fec9e-112">Per convenzione, quando si utilizza un database relazionale, viene eseguito il mapping delle proprietà delle entità alle colonne della tabella con lo stesso nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="fec9e-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="fec9e-113">Se si preferisce configurare le colonne con nomi diversi, è possibile eseguire questa operazione come frammento di codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fec9e-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-114">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-115">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="fec9e-116">Tipi di dati delle colonne</span><span class="sxs-lookup"><span data-stu-id="fec9e-116">Column data types</span></span>

<span data-ttu-id="fec9e-117">Quando si utilizza un database relazionale, il provider di database seleziona un tipo di dati basato sul tipo .NET della proprietà.</span><span class="sxs-lookup"><span data-stu-id="fec9e-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="fec9e-118">Prende in considerazione anche altri metadati, ad esempio la [lunghezza massima](#maximum-length)configurata, se la proprietà fa parte di una chiave primaria e così via.</span><span class="sxs-lookup"><span data-stu-id="fec9e-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="fec9e-119">Ad esempio, SQL Server esegue `DateTime` il mapping delle proprietà alle `datetime2(7)` colonne e `string` delle proprietà alle `nvarchar(max)` colonne (o a `nvarchar(450)` per le proprietà utilizzate come chiave).</span><span class="sxs-lookup"><span data-stu-id="fec9e-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="fec9e-120">È inoltre possibile configurare le colonne in modo da specificare un tipo di dati esatto per una colonna.</span><span class="sxs-lookup"><span data-stu-id="fec9e-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="fec9e-121">Il codice seguente, ad esempio, Configura `Url` come una stringa non Unicode con lunghezza massima `200` e `Rating` come decimale con precisione `5` e scala di `2` :</span><span class="sxs-lookup"><span data-stu-id="fec9e-121">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-122">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-123">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="fec9e-124">Lunghezza massima</span><span class="sxs-lookup"><span data-stu-id="fec9e-124">Maximum length</span></span>

<span data-ttu-id="fec9e-125">La configurazione di una lunghezza massima fornisce un suggerimento al provider di database sul tipo di dati della colonna appropriato da scegliere per una determinata proprietà.</span><span class="sxs-lookup"><span data-stu-id="fec9e-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="fec9e-126">La lunghezza massima si applica solo ai tipi di dati della matrice, ad esempio `string` e `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="fec9e-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="fec9e-127">Entity Framework non esegue alcuna convalida della lunghezza massima prima di passare i dati al provider.</span><span class="sxs-lookup"><span data-stu-id="fec9e-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="fec9e-128">È necessario che il provider o l'archivio dati venga convalidato se appropriato.</span><span class="sxs-lookup"><span data-stu-id="fec9e-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="fec9e-129">Se, ad esempio, la destinazione è SQL Server, il superamento della lunghezza massima provocherà un'eccezione poiché il tipo di dati della colonna sottostante non consentirà l'archiviazione di dati in eccesso.</span><span class="sxs-lookup"><span data-stu-id="fec9e-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="fec9e-130">Nell'esempio seguente, la configurazione di una lunghezza massima di 500 causerà la creazione di una colonna di tipo `nvarchar(500)` in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="fec9e-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-131">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-132">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a><span data-ttu-id="fec9e-133">Precisione e scala</span><span class="sxs-lookup"><span data-stu-id="fec9e-133">Precision and Scale</span></span>

<span data-ttu-id="fec9e-134">A partire da EFCore 5,0, è possibile usare l'API Fluent per configurare la precisione e la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="fec9e-134">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="fec9e-135">Indica al provider di database la quantità di spazio di archiviazione necessaria per una determinata colonna.</span><span class="sxs-lookup"><span data-stu-id="fec9e-135">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="fec9e-136">Si applica solo ai tipi di dati in cui il provider consente la precisione e la scalabilità variano, in genere `decimal` e `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="fec9e-136">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="fec9e-137">Per le `decimal` proprietà, la precisione definisce il numero massimo di cifre necessarie per esprimere qualsiasi valore che la colonna conterrà e la scalabilità definisce il numero massimo di posizioni decimali necessarie.</span><span class="sxs-lookup"><span data-stu-id="fec9e-137">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="fec9e-138">Per `DateTime` le proprietà, la precisione definisce il numero massimo di cifre necessarie per esprimere frazioni di secondi e la scala non viene utilizzata.</span><span class="sxs-lookup"><span data-stu-id="fec9e-138">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="fec9e-139">Entity Framework non esegue alcuna convalida della precisione o della scala prima di passare i dati al provider.</span><span class="sxs-lookup"><span data-stu-id="fec9e-139">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="fec9e-140">È necessario che il provider o l'archivio dati venga convalidato in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="fec9e-140">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="fec9e-141">Ad esempio, quando la destinazione è SQL Server, una colonna con tipo di dati `datetime` non consente l'impostazione della precisione, mentre una `datetime2` può avere una precisione compresa tra 0 e 7 inclusi.</span><span class="sxs-lookup"><span data-stu-id="fec9e-141">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="fec9e-142">Nell'esempio seguente, se si configura la `Score` Proprietà in modo che la precisione 14 e la scala 2 provochino la creazione di una colonna di tipo `decimal(14,2)` in SQL Server e la configurazione della `LastUpdated` Proprietà in modo che la precisione 3 provochi una colonna di tipo `datetime2(3)` :</span><span class="sxs-lookup"><span data-stu-id="fec9e-142">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-143">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-143">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="fec9e-144">Attualmente non è possibile usare le annotazioni dei dati per la configurazione.</span><span class="sxs-lookup"><span data-stu-id="fec9e-144">Currently not possible to use data annotations to configure.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-145">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-145">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="fec9e-146">La scala non viene mai definita senza prima definire la precisione, quindi l'API Fluent per la definizione della scala è `HasPrecision(precision, scale)` .</span><span class="sxs-lookup"><span data-stu-id="fec9e-146">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="fec9e-147">Proprietà obbligatorie e facoltative</span><span class="sxs-lookup"><span data-stu-id="fec9e-147">Required and optional properties</span></span>

<span data-ttu-id="fec9e-148">Una proprietà è considerata facoltativa se è valida per contenerla `null` .</span><span class="sxs-lookup"><span data-stu-id="fec9e-148">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="fec9e-149">Se `null` non è un valore valido da assegnare a una proprietà, viene considerata una proprietà obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="fec9e-149">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="fec9e-150">Quando si esegue il mapping a uno schema di database relazionale, le proprietà obbligatorie vengono create come colonne che non ammettono i valori null e le proprietà facoltative vengono create come colonne nullable.</span><span class="sxs-lookup"><span data-stu-id="fec9e-150">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="fec9e-151">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="fec9e-151">Conventions</span></span>

<span data-ttu-id="fec9e-152">Per convenzione, una proprietà il cui tipo .NET può contenere null verrà configurato come facoltativo, mentre le proprietà il cui tipo .NET non può contenere valori null verranno configurate in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="fec9e-152">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="fec9e-153">Tutte le proprietà con tipi di valore .NET ( `int` , `decimal` , e così via), ad esempio, `bool` sono configurate come obbligatorie e tutte le proprietà con tipi di valore .NET Nullable ( `int?` , `decimal?` , `bool?` e così via) sono configurate come facoltative.</span><span class="sxs-lookup"><span data-stu-id="fec9e-153">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="fec9e-154">In C# 8 è stata introdotta una nuova funzionalità denominata [tipi di riferimento Nullable](/dotnet/csharp/tutorials/nullable-reference-types), che consente di aggiungere annotazioni ai tipi di riferimento, indicando se è possibile che contengano null o meno.</span><span class="sxs-lookup"><span data-stu-id="fec9e-154">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="fec9e-155">Questa funzionalità è disabilitata per impostazione predefinita e, se abilitata, modifica il comportamento del EF Core nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fec9e-155">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="fec9e-156">Se i tipi di riferimento nullable sono disabilitati (impostazione predefinita), tutte le proprietà con i tipi di riferimento .NET vengono configurate come facoltative per convenzione, ad esempio `string` .</span><span class="sxs-lookup"><span data-stu-id="fec9e-156">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="fec9e-157">Se sono abilitati i tipi di riferimento Nullable, le proprietà verranno configurate in base al supporto di valori null C# del tipo .NET: `string?` verranno configurate come facoltative, ma `string` verranno configurate in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="fec9e-157">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="fec9e-158">Nell'esempio seguente viene illustrato un tipo di entità con proprietà obbligatorie e facoltative, con la funzionalità di riferimento Nullable disabilitata (impostazione predefinita) e abilitata:</span><span class="sxs-lookup"><span data-stu-id="fec9e-158">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="fec9e-159">Senza tipi di riferimento Nullable (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="fec9e-159">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="fec9e-160">Con tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="fec9e-160">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="fec9e-161">L'uso di tipi di riferimento Nullable è consigliato perché trasmette il supporto di valori null espresso nel codice C# al modello di EF Core e al database e evita l'uso dell'API Fluent o delle annotazioni dei dati per esprimere lo stesso concetto due volte.</span><span class="sxs-lookup"><span data-stu-id="fec9e-161">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="fec9e-162">Prestare attenzione quando si abilitano i tipi di riferimento nullable in un progetto esistente: le proprietà del tipo di riferimento che in precedenza erano configurate come facoltative ora verranno configurate come richieste, a meno che non vengano annotate in modo esplicito come Nullable.</span><span class="sxs-lookup"><span data-stu-id="fec9e-162">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="fec9e-163">Quando si gestisce uno schema di database relazionale, è possibile che vengano generate migrazioni che modificano il supporto di valori null per la colonna di database.</span><span class="sxs-lookup"><span data-stu-id="fec9e-163">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="fec9e-164">Per ulteriori informazioni sui tipi di riferimento nullable e su come utilizzarli con EF Core, [vedere la pagina della documentazione dedicata per questa funzionalità](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="fec9e-164">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="fec9e-165">Configurazione esplicita</span><span class="sxs-lookup"><span data-stu-id="fec9e-165">Explicit configuration</span></span>

<span data-ttu-id="fec9e-166">Una proprietà che sarebbe facoltativa per convenzione può essere configurata in modo da essere obbligatoria come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fec9e-166">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="fec9e-167">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fec9e-167">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="fec9e-168">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fec9e-168">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="fec9e-169">Regole di confronto delle colonne</span><span class="sxs-lookup"><span data-stu-id="fec9e-169">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="fec9e-170">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="fec9e-170">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="fec9e-171">È possibile definire regole di confronto nelle colonne di testo, determinando il modo in cui vengono confrontate e ordinate.</span><span class="sxs-lookup"><span data-stu-id="fec9e-171">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="fec9e-172">Nel frammento di codice seguente, ad esempio, viene configurata una colonna SQL Server per non fare distinzione tra maiuscole e minuscole:</span><span class="sxs-lookup"><span data-stu-id="fec9e-172">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="fec9e-173">Se tutte le colonne in un database devono utilizzare una determinata regola di confronto, definire le regole di confronto a livello di database.</span><span class="sxs-lookup"><span data-stu-id="fec9e-173">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="fec9e-174">Informazioni generali sul supporto EF Core per le regole di confronto sono disponibili nella [pagina della documentazione relativa alle regole di confronto](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="fec9e-174">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>
