---
title: Tipi di entità autofirmati-EF Core
description: Come configurare i tipi di entità autochiave utilizzando Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: d1a60e0504b22623b97c1a4963d2e3f70faa365c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429507"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="1c8a0-103">Tipi di entità senza chiave</span><span class="sxs-lookup"><span data-stu-id="1c8a0-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="1c8a0-104">Questa funzionalità è stata aggiunta con il nome dei tipi di query.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-104">This feature was added under the name of query types.</span></span> <span data-ttu-id="1c8a0-105">In EF Core 3,0 il concetto è stato rinominato in tipi di entità senza chiave.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="1c8a0-106">L' `[Keyless]` annotazione dei dati è diventata disponibile in EFCore 5,0.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="1c8a0-107">Oltre ai normali tipi di entità, un modello di EF Core può contenere _tipi di entità_ senza chiave, che possono essere usati per eseguire query di database su dati che non contengono valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_ , which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="1c8a0-108">Definizione di tipi di entità autochiave</span><span class="sxs-lookup"><span data-stu-id="1c8a0-108">Defining Keyless entity types</span></span>

<span data-ttu-id="1c8a0-109">È possibile definire i tipi di entità con chiave fissa usando l'annotazione dei dati o l'API Fluent:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="1c8a0-110">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="1c8a0-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="1c8a0-111">API Fluent</span><span class="sxs-lookup"><span data-stu-id="1c8a0-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="1c8a0-112">Caratteristiche di tipi di entità autochiave</span><span class="sxs-lookup"><span data-stu-id="1c8a0-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="1c8a0-113">I tipi di entità autonome supportano molte delle stesse funzionalità di mapping dei normali tipi di entità, ad esempio il mapping di ereditarietà e le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="1c8a0-114">Negli archivi relazionali è possibile configurare gli oggetti e le colonne di database di destinazione tramite metodi API Fluent o annotazioni di dati.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="1c8a0-115">Tuttavia, sono diversi dai normali tipi di entità in quanto:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="1c8a0-116">Non è possibile definire una chiave.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="1c8a0-117">Non vengono mai rilevate per le modifiche apportate in _DbContext_ e pertanto non vengono mai inserite, aggiornate o eliminate nel database.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="1c8a0-118">Non vengono mai individuati per convenzione.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="1c8a0-119">Supporta solo un subset di funzionalità di mapping di navigazione, in particolare:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="1c8a0-120">Non possono mai fungere da entità finale principale di una relazione.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="1c8a0-121">Potrebbero non avere spostamenti sulle entità di proprietà</span><span class="sxs-lookup"><span data-stu-id="1c8a0-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="1c8a0-122">Possono contenere solo proprietà di navigazione di riferimento che puntano a entità regolari.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="1c8a0-123">Le entità non possono contenere proprietà di navigazione per i tipi di entità autochiave.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="1c8a0-124">Deve essere configurato con un' `[Keyless]` annotazione dati o una `.HasNoKey()` chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="1c8a0-125">Può essere mappato a una _query di definizione_.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="1c8a0-126">Una query di definizione è una query dichiarata nel modello che funge da origine dati per un tipo di entità autonome.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="1c8a0-127">Scenari di utilizzo</span><span class="sxs-lookup"><span data-stu-id="1c8a0-127">Usage scenarios</span></span>

<span data-ttu-id="1c8a0-128">Di seguito sono riportati alcuni degli scenari di utilizzo principali per i tipi di entità autochiave:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="1c8a0-129">Fungendo da tipo restituito per le [query SQL non elaborate](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="1c8a0-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="1c8a0-130">Mapping a viste di database che non contengono una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="1c8a0-131">Mapping a tabelle in cui non è definita una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="1c8a0-132">Mapping a query definite nel modello.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="1c8a0-133">Mapping a oggetti di database</span><span class="sxs-lookup"><span data-stu-id="1c8a0-133">Mapping to database objects</span></span>

<span data-ttu-id="1c8a0-134">Il mapping di un tipo di entità autochiave a un oggetto di database viene eseguito tramite l' `ToTable` `ToView` API o Fluent.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="1c8a0-135">Dal punto di vista del EF Core, l'oggetto di database specificato in questo metodo è una _vista_ , ovvero viene considerato come un'origine di query di sola lettura e non può essere la destinazione di operazioni di aggiornamento, inserimento o eliminazione.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-135">From the perspective of EF Core, the database object specified in this method is a _view_ , meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="1c8a0-136">Tuttavia, ciò non significa che l'oggetto di database debba essere effettivamente una vista di database.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="1c8a0-137">In alternativa, può essere una tabella di database che verrà considerata di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="1c8a0-138">Viceversa, per i tipi di entità regolari, EF Core presuppone che un oggetto di database specificato nel `ToTable` metodo possa essere considerato come una _tabella_ , ovvero può essere utilizzato come origine della query, ma anche come destinazione da operazioni di aggiornamento, eliminazione e inserimento.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_ , meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="1c8a0-139">In realtà, è possibile specificare il nome di una vista di database in `ToTable` e tutto dovrebbe funzionare correttamente purché la vista sia configurata per essere aggiornabile nel database.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="1c8a0-140">`ToView` presuppone che l'oggetto sia già presente nel database e non venga creato dalle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="1c8a0-141">Esempio</span><span class="sxs-lookup"><span data-stu-id="1c8a0-141">Example</span></span>

<span data-ttu-id="1c8a0-142">Nell'esempio seguente viene illustrato come utilizzare i tipi di entità autochiave per eseguire una query su una vista di database.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="1c8a0-143">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="1c8a0-144">In primo luogo, viene definito un semplice modello di Blog e post:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="1c8a0-145">Viene quindi definita una semplice vista di database che consente di eseguire una query sul numero di post associati a ogni Blog:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="1c8a0-146">Viene quindi definita una classe che conterrà il risultato della vista di database:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="1c8a0-147">Successivamente, si configura il tipo di entità autochiave in _OnModelCreating_ usando l' `HasNoKey` API.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="1c8a0-148">Si usa l'API di configurazione Fluent per configurare il mapping per il tipo di entità autochiave:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="1c8a0-149">Successivamente, viene configurato `DbContext` per includere `DbSet<T>` :</span><span class="sxs-lookup"><span data-stu-id="1c8a0-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="1c8a0-150">Infine, è possibile eseguire una query sulla vista di database nel modo standard:</span><span class="sxs-lookup"><span data-stu-id="1c8a0-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="1c8a0-151">Si noti che è stata definita anche una proprietà di query a livello di contesto (DbSet) che funge da radice per le query su questo tipo.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>

> [!TIP]
> <span data-ttu-id="1c8a0-152">Per testare i tipi di entità senza chiave mappati alle viste usando il provider in memoria, eseguirne il mapping a una query tramite `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="1c8a0-152">To test keyless entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="1c8a0-153">Per altri dettagli, vedere un [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) usando questa tecnica.</span><span class="sxs-lookup"><span data-stu-id="1c8a0-153">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
