---
title: Chiavi-EF Core
description: Come configurare le chiavi per i tipi di entità quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/keys
ms.openlocfilehash: c79ab0445e80b0b6f4a8b49ef0d4c063bf938851
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983533"
---
# <a name="keys"></a><span data-ttu-id="40dc7-103">Chiavi</span><span class="sxs-lookup"><span data-stu-id="40dc7-103">Keys</span></span>

<span data-ttu-id="40dc7-104">Una chiave funge da identificatore univoco per ogni istanza dell'entità.</span><span class="sxs-lookup"><span data-stu-id="40dc7-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="40dc7-105">Per la maggior parte delle entità in EF è disponibile un'unica chiave, che esegue il mapping al concetto di *chiave primaria* nei database relazionali (per le entità senza chiavi, vedere [entità](xref:core/modeling/keyless-entity-types)senza chiave).</span><span class="sxs-lookup"><span data-stu-id="40dc7-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="40dc7-106">Le entità possono avere chiavi aggiuntive oltre la chiave primaria. per ulteriori informazioni, vedere [chiavi alternative](#alternate-keys) .</span><span class="sxs-lookup"><span data-stu-id="40dc7-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

## <a name="configuring-a-primary-key"></a><span data-ttu-id="40dc7-107">Configurazione di una chiave primaria</span><span class="sxs-lookup"><span data-stu-id="40dc7-107">Configuring a primary key</span></span>

<span data-ttu-id="40dc7-108">Per convenzione, una proprietà denominata `Id` o `<type name>Id` verrà configurata come chiave primaria di un'entità.</span><span class="sxs-lookup"><span data-stu-id="40dc7-108">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="40dc7-109">I [tipi di entità di proprietà](xref:core/modeling/owned-entities) utilizzano regole diverse per definire le chiavi.</span><span class="sxs-lookup"><span data-stu-id="40dc7-109">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="40dc7-110">È possibile configurare una singola proprietà come chiave primaria di un'entità come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="40dc7-110">You can configure a single property to be the primary key of an entity as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="40dc7-111">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="40dc7-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="40dc7-112">API Fluent</span><span class="sxs-lookup"><span data-stu-id="40dc7-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="40dc7-113">È anche possibile configurare più proprietà come chiave di un'entità. questa operazione è nota come chiave composta.</span><span class="sxs-lookup"><span data-stu-id="40dc7-113">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="40dc7-114">Le chiavi composite possono essere configurate solo usando l'API Fluent; le convenzioni non configurano mai una chiave composta e non è possibile usare le annotazioni dei dati per configurarne una.</span><span class="sxs-lookup"><span data-stu-id="40dc7-114">Composite keys can only be configured using the Fluent API; conventions will never set up a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="value-generation"></a><span data-ttu-id="40dc7-115">Generazione di valore</span><span class="sxs-lookup"><span data-stu-id="40dc7-115">Value generation</span></span>

<span data-ttu-id="40dc7-116">Per le chiavi primarie GUID e numerici non compositi, EF Core imposta la generazione di valori per convenzione.</span><span class="sxs-lookup"><span data-stu-id="40dc7-116">For non-composite numeric and GUID primary keys, EF Core sets up value generation for you by convention.</span></span> <span data-ttu-id="40dc7-117">Una chiave primaria numerica in SQL Server, ad esempio, viene configurata automaticamente come colonna IDENTITY.</span><span class="sxs-lookup"><span data-stu-id="40dc7-117">For example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span> <span data-ttu-id="40dc7-118">Per ulteriori informazioni, vedere [la documentazione relativa alla generazione dei valori](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="40dc7-118">For more information, see [the documentation on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="primary-key-name"></a><span data-ttu-id="40dc7-119">Nome chiave primaria</span><span class="sxs-lookup"><span data-stu-id="40dc7-119">Primary key name</span></span>

<span data-ttu-id="40dc7-120">Per convenzione, le chiavi primarie dei database relazionali vengono create con il nome `PK_<type name>` .</span><span class="sxs-lookup"><span data-stu-id="40dc7-120">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="40dc7-121">È possibile configurare il nome del vincolo PRIMARY KEY come segue:</span><span class="sxs-lookup"><span data-stu-id="40dc7-121">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="40dc7-122">Tipi di chiave e valori</span><span class="sxs-lookup"><span data-stu-id="40dc7-122">Key types and values</span></span>

<span data-ttu-id="40dc7-123">Mentre EF Core supporta l'utilizzo di proprietà di qualsiasi tipo primitivo come chiave primaria, tra cui `string` , `Guid` `byte[]` e altri, non tutti i database supportano tutti i tipi come chiavi.</span><span class="sxs-lookup"><span data-stu-id="40dc7-123">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="40dc7-124">In alcuni casi i valori di chiave possono essere convertiti automaticamente in un tipo supportato. in caso contrario, la conversione deve essere [specificata manualmente](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="40dc7-124">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="40dc7-125">Quando si aggiunge una nuova entità al contesto, le proprietà chiave devono sempre avere un valore non predefinito, ma alcuni tipi verranno [generati dal database](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="40dc7-125">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="40dc7-126">In tal caso, EF tenterà di generare un valore temporaneo quando l'entità viene aggiunta per finalità di rilevamento.</span><span class="sxs-lookup"><span data-stu-id="40dc7-126">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="40dc7-127">Dopo la chiamata a [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) , il valore temporaneo verrà sostituito dal valore generato dal database.</span><span class="sxs-lookup"><span data-stu-id="40dc7-127">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="40dc7-128">Se una proprietà chiave ha il valore generato dal database e viene specificato un valore non predefinito quando viene aggiunta un'entità, Entity Framework presuppone che l'entità esista già nel database e tenterà di aggiornarla invece di inserirne una nuova.</span><span class="sxs-lookup"><span data-stu-id="40dc7-128">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="40dc7-129">Per evitare questo problema, disabilitare la generazione di valori o vedere [come specificare valori espliciti per le proprietà generate](xref:core/modeling/generated-properties#overriding-value-generation).</span><span class="sxs-lookup"><span data-stu-id="40dc7-129">To avoid this, turn off value generation or see [how to specify explicit values for generated properties](xref:core/modeling/generated-properties#overriding-value-generation).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="40dc7-130">Chiavi alternative</span><span class="sxs-lookup"><span data-stu-id="40dc7-130">Alternate Keys</span></span>

<span data-ttu-id="40dc7-131">Una chiave alternativa funge da identificatore univoco alternativo per ogni istanza di entità oltre alla chiave primaria. può essere usato come destinazione di una relazione.</span><span class="sxs-lookup"><span data-stu-id="40dc7-131">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="40dc7-132">Quando si utilizza un database relazionale, viene eseguito il mapping al concetto di indice/vincolo univoco nelle colonne chiave alternative e di uno o più vincoli di chiave esterna che fanno riferimento alle colonne.</span><span class="sxs-lookup"><span data-stu-id="40dc7-132">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="40dc7-133">Se si vuole semplicemente applicare l'univocità in una colonna, definire un indice univoco anziché una chiave alternativa (vedere [indici](xref:core/modeling/indexes)).</span><span class="sxs-lookup"><span data-stu-id="40dc7-133">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](xref:core/modeling/indexes)).</span></span> <span data-ttu-id="40dc7-134">In EF le chiavi alternative sono di sola lettura e forniscono una semantica aggiuntiva sugli indici univoci, perché possono essere utilizzate come destinazione di una chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="40dc7-134">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="40dc7-135">Quando necessario, vengono in genere introdotte chiavi alternative e non è necessario configurarle manualmente.</span><span class="sxs-lookup"><span data-stu-id="40dc7-135">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="40dc7-136">Per convenzione, viene introdotta una chiave alternativa quando si identifica una proprietà che non è la chiave primaria come destinazione di una relazione.</span><span class="sxs-lookup"><span data-stu-id="40dc7-136">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="40dc7-137">È anche possibile configurare una singola proprietà in modo che sia una chiave alternativa:</span><span class="sxs-lookup"><span data-stu-id="40dc7-137">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="40dc7-138">È anche possibile configurare più proprietà in modo che siano una chiave alternativa (nota come chiave alternativa composta):</span><span class="sxs-lookup"><span data-stu-id="40dc7-138">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="40dc7-139">Infine, per convenzione, gli indici e i vincoli introdotti per una chiave alternativa verranno denominati `AK_<type name>_<property name>` (per le chiavi alternative composite `<property name>` diventa un elenco di nomi di proprietà separati da un carattere di sottolineatura).</span><span class="sxs-lookup"><span data-stu-id="40dc7-139">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="40dc7-140">È possibile configurare il nome dell'indice e del vincolo UNIQUE della chiave alternativa:</span><span class="sxs-lookup"><span data-stu-id="40dc7-140">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
