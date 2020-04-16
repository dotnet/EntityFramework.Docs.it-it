---
title: Tipi di entità senza chiave - Entity Framework CoreKeyless Entity Types - EF Core
description: Come configurare i tipi di entità senza chiave utilizzando Entity Framework CoreHow to configure keyless entity types using Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434214"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="9162d-103">Tipi di entità senza chiave</span><span class="sxs-lookup"><span data-stu-id="9162d-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="9162d-104">Questa funzionalità è stata aggiunta in EF Core 2.1 con il nome dei tipi di query.</span><span class="sxs-lookup"><span data-stu-id="9162d-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="9162d-105">In Entity Framework Core 3.0 il concetto è stato rinominato in tipi di entità senza chiave.</span><span class="sxs-lookup"><span data-stu-id="9162d-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="9162d-106">Oltre ai tipi di entità regolari, un modello di Entity Framework Core può contenere tipi di _entità senza chiave_, che possono essere utilizzati per eseguire query di database su dati che non contengono valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="9162d-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="9162d-107">Definizione di tipi di entità senza chiaveDefining Keyless entity types</span><span class="sxs-lookup"><span data-stu-id="9162d-107">Defining Keyless entity types</span></span>

<span data-ttu-id="9162d-108">I tipi di entità senza chiave possono essere definiti usando l'annotazione dei dati o l'API Fluent:Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span><span class="sxs-lookup"><span data-stu-id="9162d-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9162d-109">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="9162d-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="9162d-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="9162d-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="9162d-111">Caratteristiche dei tipi di entità senza chiaveKeyless entity types characteristics</span><span class="sxs-lookup"><span data-stu-id="9162d-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="9162d-112">I tipi di entità senza chiave supportano molte delle stesse funzionalità di mapping dei tipi di entità regolari, ad esempio il mapping di ereditarietà e le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9162d-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="9162d-113">Negli archivi relazionali possono configurare le colonne e gli oggetti di database di destinazione tramite metodi API fluenti o annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="9162d-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="9162d-114">Tuttavia, sono diversi dai tipi di entità regolari in quanto:However, they are different from regular entity types in that they:</span><span class="sxs-lookup"><span data-stu-id="9162d-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="9162d-115">Non può avere una chiave definita.</span><span class="sxs-lookup"><span data-stu-id="9162d-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="9162d-116">Non vengono mai rilevati per le modifiche in _DbContext_ e pertanto non vengono mai inseriti, aggiornati o eliminati nel database.</span><span class="sxs-lookup"><span data-stu-id="9162d-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="9162d-117">Non vengono mai scoperti per convenzione.</span><span class="sxs-lookup"><span data-stu-id="9162d-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="9162d-118">Supporta solo un sottoinsieme delle funzionalità di mapping di navigazione, in particolare:Only support a subset of navigation mapping capabilities, specifically:</span><span class="sxs-lookup"><span data-stu-id="9162d-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="9162d-119">Non possono mai agire come la fine principale di una relazione.</span><span class="sxs-lookup"><span data-stu-id="9162d-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="9162d-120">Potrebbero non avere spostamenti verso entità di proprietà</span><span class="sxs-lookup"><span data-stu-id="9162d-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="9162d-121">Possono contenere solo proprietà di navigazione di riferimento che puntano a entità regolari.</span><span class="sxs-lookup"><span data-stu-id="9162d-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="9162d-122">Le entità non possono contenere proprietà di navigazione per i tipi di entità senza chiave.</span><span class="sxs-lookup"><span data-stu-id="9162d-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="9162d-123">È necessario essere `[Keyless]` configurati con `.HasNoKey()` un'annotazione dati o una chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="9162d-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="9162d-124">Può essere mappato a una query di _definizione._</span><span class="sxs-lookup"><span data-stu-id="9162d-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="9162d-125">Una query di definizione è una query dichiarata nel modello che funge da origine dati per un tipo di entità senza chiave.</span><span class="sxs-lookup"><span data-stu-id="9162d-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="9162d-126">Scenari di utilizzo</span><span class="sxs-lookup"><span data-stu-id="9162d-126">Usage scenarios</span></span>

<span data-ttu-id="9162d-127">Alcuni degli scenari di utilizzo principali per i tipi di entità senza chiave sono:Some of the main usage scenarios for keyless entity types are:</span><span class="sxs-lookup"><span data-stu-id="9162d-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="9162d-128">Funge da tipo restituito per [le query SQL non elaborate.](xref:core/querying/raw-sql)</span><span class="sxs-lookup"><span data-stu-id="9162d-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="9162d-129">Mapping a viste di database che non contengono una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="9162d-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="9162d-130">Mapping a tabelle per le cui tabelle non è stata definita una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="9162d-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="9162d-131">Mapping alle query definite nel modello.</span><span class="sxs-lookup"><span data-stu-id="9162d-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="9162d-132">Mapping a oggetti di database</span><span class="sxs-lookup"><span data-stu-id="9162d-132">Mapping to database objects</span></span>

<span data-ttu-id="9162d-133">Il mapping di un tipo di entità `ToTable` senza `ToView` chiave a un oggetto di database viene ottenuto utilizzando l'API o fluent.</span><span class="sxs-lookup"><span data-stu-id="9162d-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="9162d-134">Dal punto di vista di EF Core, l'oggetto di database specificato in questo metodo è una _visualizzazione_, ovvero viene considerato come un'origine di query di sola lettura e non può essere la destinazione delle operazioni di aggiornamento, inserimento o eliminazione.</span><span class="sxs-lookup"><span data-stu-id="9162d-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="9162d-135">Tuttavia, ciò non significa che l'oggetto di database è effettivamente necessario per essere una vista di database.</span><span class="sxs-lookup"><span data-stu-id="9162d-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="9162d-136">In alternativa può essere una tabella di database che verrà considerata di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="9162d-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="9162d-137">Al contrario, per i tipi di entità regolari, Entity `ToTable` Framework Core presuppone che un oggetto di database specificato nel metodo può essere considerato come una _tabella_, ovvero può essere utilizzato come origine di query, ma anche destinato da operazioni di aggiornamento, eliminazione e inserimento.</span><span class="sxs-lookup"><span data-stu-id="9162d-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="9162d-138">In effetti, è possibile specificare il `ToTable` nome di una vista di database in e tutto dovrebbe funzionare correttamente finché la vista è configurata per essere aggiornabile sul database.</span><span class="sxs-lookup"><span data-stu-id="9162d-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="9162d-139">`ToView`presuppone che l'oggetto esista già nel database e che non verrà creato dalle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="9162d-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="9162d-140">Esempio</span><span class="sxs-lookup"><span data-stu-id="9162d-140">Example</span></span>

<span data-ttu-id="9162d-141">Nell'esempio seguente viene illustrato come utilizzare i tipi di entità senza chiave per eseguire una query su una vista di database.</span><span class="sxs-lookup"><span data-stu-id="9162d-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="9162d-142">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="9162d-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="9162d-143">In primo luogo, definiamo un semplice modello di Blog e Post:</span><span class="sxs-lookup"><span data-stu-id="9162d-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="9162d-144">Successivamente, definiamo una semplice vista di database che ci permetterà di interrogare il numero di post associati a ogni blog:</span><span class="sxs-lookup"><span data-stu-id="9162d-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="9162d-145">Successivamente, definiamo una classe per contenere il risultato dalla vista del database:Next, we define a class to hold the result from the database view:</span><span class="sxs-lookup"><span data-stu-id="9162d-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="9162d-146">Successivamente, si configura il tipo di entità `HasNoKey` senza chiave in _OnModelCreating_ usando l'API.</span><span class="sxs-lookup"><span data-stu-id="9162d-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="9162d-147">Usiamo l'API di configurazione fluente per configurare il mapping per il tipo di entità keyless:We use fluent configuration API to configure the mapping for the keyless entity type:</span><span class="sxs-lookup"><span data-stu-id="9162d-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="9162d-148">Successivamente, configuriamo `DbContext` il `DbSet<T>`per includere il :</span><span class="sxs-lookup"><span data-stu-id="9162d-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="9162d-149">Infine, è possibile eseguire una query sulla vista del database nel modo standard:</span><span class="sxs-lookup"><span data-stu-id="9162d-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="9162d-150">Si noti che è stata definita anche una proprietà di query a livello di contesto (DbSet) che funge da radice per le query su questo tipo.</span><span class="sxs-lookup"><span data-stu-id="9162d-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
