---
title: Proprietà entità-EF Core
description: Come configurare e mappare le proprietà di un'entità usando Entity Framework Core
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: e4a1867a90df1fb277e7dd44b93d6c2d47895030
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664156"
---
# <a name="entity-properties"></a><span data-ttu-id="1d89e-103">Proprietà delle entità</span><span class="sxs-lookup"><span data-stu-id="1d89e-103">Entity Properties</span></span>

<span data-ttu-id="1d89e-104">Ogni tipo di entità nel modello dispone di un set di proprietà, che EF Core leggerà e scriverà dal database.</span><span class="sxs-lookup"><span data-stu-id="1d89e-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="1d89e-105">Se si usa un database relazionale, le proprietà dell'entità vengono mappate alle colonne della tabella.</span><span class="sxs-lookup"><span data-stu-id="1d89e-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="1d89e-106">Proprietà incluse ed escluse</span><span class="sxs-lookup"><span data-stu-id="1d89e-106">Included and excluded properties</span></span>

<span data-ttu-id="1d89e-107">Per convenzione, tutte le proprietà pubbliche con un getter e un setter verranno incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="1d89e-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="1d89e-108">Le proprietà specifiche possono essere escluse come segue:</span><span class="sxs-lookup"><span data-stu-id="1d89e-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="1d89e-109">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1d89e-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="1d89e-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1d89e-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="1d89e-111">Nome colonna</span><span class="sxs-lookup"><span data-stu-id="1d89e-111">Column names</span></span>

<span data-ttu-id="1d89e-112">Per convenzione, quando si utilizza un database relazionale, viene eseguito il mapping delle proprietà delle entità alle colonne della tabella con lo stesso nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="1d89e-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="1d89e-113">Se si preferisce configurare le colonne con nomi diversi, è possibile procedere come segue:</span><span class="sxs-lookup"><span data-stu-id="1d89e-113">If you prefer to configure your columns with different names, you can do so as following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="1d89e-114">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1d89e-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="1d89e-115">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1d89e-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="1d89e-116">Tipi di dati delle colonne</span><span class="sxs-lookup"><span data-stu-id="1d89e-116">Column data types</span></span>

<span data-ttu-id="1d89e-117">Quando si utilizza un database relazionale, il provider di database seleziona un tipo di dati basato sul tipo .NET della proprietà.</span><span class="sxs-lookup"><span data-stu-id="1d89e-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="1d89e-118">Prende in considerazione anche altri metadati, ad esempio la [lunghezza massima](#maximum-length)configurata, se la proprietà fa parte di una chiave primaria e così via.</span><span class="sxs-lookup"><span data-stu-id="1d89e-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="1d89e-119">Ad esempio, SQL Server esegue `DateTime` il mapping delle proprietà alle `datetime2(7)` colonne e `string` delle proprietà alle `nvarchar(max)` colonne (o a `nvarchar(450)` per le proprietà utilizzate come chiave).</span><span class="sxs-lookup"><span data-stu-id="1d89e-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="1d89e-120">È inoltre possibile configurare le colonne in modo da specificare un tipo di dati esatto per una colonna.</span><span class="sxs-lookup"><span data-stu-id="1d89e-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="1d89e-121">Il codice seguente, ad esempio, consente di configurare `Url` come stringa non Unicode con lunghezza massima `200` e `Rating` come decimale con precisione `5` e scala di `2` :</span><span class="sxs-lookup"><span data-stu-id="1d89e-121">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="1d89e-122">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1d89e-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="1d89e-123">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1d89e-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="1d89e-124">Lunghezza massima</span><span class="sxs-lookup"><span data-stu-id="1d89e-124">Maximum length</span></span>

<span data-ttu-id="1d89e-125">La configurazione di una lunghezza massima fornisce un suggerimento al provider di database sul tipo di dati della colonna appropriato da scegliere per una determinata proprietà.</span><span class="sxs-lookup"><span data-stu-id="1d89e-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="1d89e-126">La lunghezza massima si applica solo ai tipi di dati della matrice, ad esempio `string` e `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="1d89e-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="1d89e-127">Entity Framework non esegue alcuna convalida della lunghezza massima prima di passare i dati al provider.</span><span class="sxs-lookup"><span data-stu-id="1d89e-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="1d89e-128">È necessario che il provider o l'archivio dati venga convalidato se appropriato.</span><span class="sxs-lookup"><span data-stu-id="1d89e-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="1d89e-129">Se, ad esempio, la destinazione è SQL Server, il superamento della lunghezza massima provocherà un'eccezione poiché il tipo di dati della colonna sottostante non consentirà l'archiviazione di dati in eccesso.</span><span class="sxs-lookup"><span data-stu-id="1d89e-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="1d89e-130">Nell'esempio seguente, la configurazione di una lunghezza massima di 500 causerà la creazione di una colonna di tipo `nvarchar(500)` in SQL Server:</span><span class="sxs-lookup"><span data-stu-id="1d89e-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="1d89e-131">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1d89e-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="1d89e-132">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1d89e-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="1d89e-133">Proprietà obbligatorie e facoltative</span><span class="sxs-lookup"><span data-stu-id="1d89e-133">Required and optional properties</span></span>

<span data-ttu-id="1d89e-134">Una proprietà è considerata facoltativa se è valida per contenerla `null` .</span><span class="sxs-lookup"><span data-stu-id="1d89e-134">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="1d89e-135">Se `null` non è un valore valido da assegnare a una proprietà, viene considerata una proprietà obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="1d89e-135">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="1d89e-136">Quando si esegue il mapping a uno schema di database relazionale, le proprietà obbligatorie vengono create come colonne che non ammettono i valori null e le proprietà facoltative vengono create come colonne nullable.</span><span class="sxs-lookup"><span data-stu-id="1d89e-136">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="1d89e-137">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="1d89e-137">Conventions</span></span>

<span data-ttu-id="1d89e-138">Per convenzione, una proprietà il cui tipo .NET può contenere null verrà configurato come facoltativo, mentre le proprietà il cui tipo .NET non può contenere valori null verranno configurate in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="1d89e-138">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="1d89e-139">Tutte le proprietà con tipi di valore .NET ( `int` , `decimal` , e così via), ad esempio, `bool` sono configurate come obbligatorie e tutte le proprietà con tipi di valore .NET Nullable ( `int?` , `decimal?` , `bool?` e così via) sono configurate come facoltative.</span><span class="sxs-lookup"><span data-stu-id="1d89e-139">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="1d89e-140">In C# 8 è stata introdotta una nuova funzionalità denominata [tipi di riferimento Nullable](/dotnet/csharp/tutorials/nullable-reference-types), che consente di aggiungere annotazioni ai tipi di riferimento, indicando se è possibile che contengano null o meno.</span><span class="sxs-lookup"><span data-stu-id="1d89e-140">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="1d89e-141">Questa funzionalità è disabilitata per impostazione predefinita e, se abilitata, modifica il comportamento del EF Core nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="1d89e-141">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="1d89e-142">Se i tipi di riferimento nullable sono disabilitati (impostazione predefinita), tutte le proprietà con i tipi di riferimento .NET vengono configurate come facoltative per convenzione, ad esempio `string` .</span><span class="sxs-lookup"><span data-stu-id="1d89e-142">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="1d89e-143">Se sono abilitati i tipi di riferimento Nullable, le proprietà verranno configurate in base al supporto di valori null C# del tipo .NET: `string?` verranno configurate come facoltative, mentre verranno configurate in base alle `string` esigenze.</span><span class="sxs-lookup"><span data-stu-id="1d89e-143">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="1d89e-144">Nell'esempio seguente viene illustrato un tipo di entità con proprietà obbligatorie e facoltative, con la funzionalità di riferimento Nullable disabilitata (impostazione predefinita) e abilitata:</span><span class="sxs-lookup"><span data-stu-id="1d89e-144">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="1d89e-145">Senza tipi di riferimento Nullable (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="1d89e-145">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="1d89e-146">Con tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="1d89e-146">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="1d89e-147">L'uso di tipi di riferimento Nullable è consigliato perché trasmette il supporto di valori null espresso nel codice C# al modello di EF Core e al database e evita l'uso dell'API Fluent o delle annotazioni dei dati per esprimere lo stesso concetto due volte.</span><span class="sxs-lookup"><span data-stu-id="1d89e-147">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="1d89e-148">Prestare attenzione quando si abilitano i tipi di riferimento nullable in un progetto esistente: le proprietà del tipo di riferimento che in precedenza erano configurate come facoltative ora verranno configurate come richieste, a meno che non vengano annotate in modo esplicito come Nullable.</span><span class="sxs-lookup"><span data-stu-id="1d89e-148">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="1d89e-149">Quando si gestisce uno schema di database relazionale, è possibile che vengano generate migrazioni che modificano il supporto di valori null per la colonna di database.</span><span class="sxs-lookup"><span data-stu-id="1d89e-149">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="1d89e-150">Per ulteriori informazioni sui tipi di riferimento nullable e su come utilizzarli con EF Core, [vedere la pagina della documentazione dedicata per questa funzionalità](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="1d89e-150">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="1d89e-151">Configurazione esplicita</span><span class="sxs-lookup"><span data-stu-id="1d89e-151">Explicit configuration</span></span>

<span data-ttu-id="1d89e-152">Una proprietà che sarebbe facoltativa per convenzione può essere configurata in modo da essere obbligatoria come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="1d89e-152">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="1d89e-153">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1d89e-153">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="1d89e-154">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1d89e-154">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="1d89e-155">Regole di confronto delle colonne</span><span class="sxs-lookup"><span data-stu-id="1d89e-155">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="1d89e-156">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="1d89e-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="1d89e-157">È possibile definire regole di confronto nelle colonne di testo, determinando il modo in cui vengono confrontate e ordinate.</span><span class="sxs-lookup"><span data-stu-id="1d89e-157">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="1d89e-158">Ad esempio, di seguito viene configurata una colonna SQL Server per non fare distinzione tra maiuscole e minuscole:</span><span class="sxs-lookup"><span data-stu-id="1d89e-158">For example, the following configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="1d89e-159">Se tutte le colonne in un database devono utilizzare una determinata regola di confronto, definire le regole di confronto a livello di database.</span><span class="sxs-lookup"><span data-stu-id="1d89e-159">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="1d89e-160">Informazioni generali sul supporto EF Core per le regole di confronto sono disponibili nella [pagina della documentazione relativa alle regole di confronto](xref:core/miscellaneous/collations-and-case-sensitivity.md).</span><span class="sxs-lookup"><span data-stu-id="1d89e-160">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity.md).</span></span>