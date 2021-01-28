---
title: Valori generati-EF Core
description: Come configurare la generazione di valori per le proprietà quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983547"
---
# <a name="generated-values"></a><span data-ttu-id="83b9c-103">Valori generati</span><span class="sxs-lookup"><span data-stu-id="83b9c-103">Generated Values</span></span>

<span data-ttu-id="83b9c-104">È possibile generare valori per le colonne del database in diversi modi: le colonne chiave primaria sono spesso numeri interi a incremento automatico, altre colonne hanno valori predefiniti o calcolati e così via. In questa pagina vengono illustrati i vari modelli per la generazione di valori di configurazione con EF Core.</span><span class="sxs-lookup"><span data-stu-id="83b9c-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="83b9c-105">Valori predefiniti</span><span class="sxs-lookup"><span data-stu-id="83b9c-105">Default values</span></span>

<span data-ttu-id="83b9c-106">Nei database relazionali, una colonna può essere configurata con un valore predefinito. Se viene inserita una riga senza un valore per tale colonna, verrà utilizzato il valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="83b9c-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="83b9c-107">È possibile configurare un valore predefinito in una proprietà:</span><span class="sxs-lookup"><span data-stu-id="83b9c-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="83b9c-108">È anche possibile specificare un frammento SQL usato per calcolare il valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="83b9c-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="83b9c-109">Colonne calcolate</span><span class="sxs-lookup"><span data-stu-id="83b9c-109">Computed columns</span></span>

<span data-ttu-id="83b9c-110">Nella maggior parte dei database relazionali, una colonna può essere configurata in modo che il relativo valore venga calcolato nel database, in genere con un'espressione che fa riferimento ad altre colonne:</span><span class="sxs-lookup"><span data-stu-id="83b9c-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="83b9c-111">Il precedente crea una colonna calcolata *virtuale* , il cui valore viene calcolato ogni volta che viene recuperato dal database.</span><span class="sxs-lookup"><span data-stu-id="83b9c-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="83b9c-112">È inoltre possibile specificare che una colonna calcolata deve essere *archiviata* (talvolta denominata *permanente*), ovvero viene calcolata a ogni aggiornamento della riga e viene archiviata su disco insieme a colonne normali:</span><span class="sxs-lookup"><span data-stu-id="83b9c-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="83b9c-113">Il supporto per la creazione di colonne calcolate archiviate è stato aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="83b9c-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="83b9c-114">Chiavi primarie</span><span class="sxs-lookup"><span data-stu-id="83b9c-114">Primary keys</span></span>

<span data-ttu-id="83b9c-115">Per convenzione, le chiavi primarie non composite di tipo short, int, Long o GUID sono impostate in modo da generare valori per le entità inserite se un valore non viene fornito dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="83b9c-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="83b9c-116">Il provider di database gestisce in genere la configurazione necessaria. una chiave primaria numerica in SQL Server, ad esempio, viene configurata automaticamente come colonna IDENTITY.</span><span class="sxs-lookup"><span data-stu-id="83b9c-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="83b9c-117">Per ulteriori informazioni, [vedere la documentazione sulle chiavi](xref:core/modeling/keys).</span><span class="sxs-lookup"><span data-stu-id="83b9c-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="83b9c-118">Configurazione esplicita della generazione di valori</span><span class="sxs-lookup"><span data-stu-id="83b9c-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="83b9c-119">Abbiamo visto sopra che EF Core imposta automaticamente la generazione di valori per le chiavi primarie, ma è consigliabile eseguire la stessa operazione per le proprietà non chiave.</span><span class="sxs-lookup"><span data-stu-id="83b9c-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="83b9c-120">È possibile configurare qualsiasi proprietà in modo che il relativo valore venga generato per le entità inserite come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="83b9c-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="83b9c-121">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="83b9c-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="83b9c-122">API Fluent</span><span class="sxs-lookup"><span data-stu-id="83b9c-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="83b9c-123">Analogamente, una proprietà può essere configurata in modo da generare il relativo valore in Aggiungi o aggiorna:</span><span class="sxs-lookup"><span data-stu-id="83b9c-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="83b9c-124">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="83b9c-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="83b9c-125">API Fluent</span><span class="sxs-lookup"><span data-stu-id="83b9c-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

<span data-ttu-id="83b9c-126">\*\*_</span><span class="sxs-lookup"><span data-stu-id="83b9c-126">\*\*_</span></span>

> [!WARNING]
> <span data-ttu-id="83b9c-127">Diversamente da quanto avviene con i valori predefiniti o le colonne calcolate, non viene specificato _how \* i valori da generare. dipende dal provider di database utilizzato.</span><span class="sxs-lookup"><span data-stu-id="83b9c-127">Unlike with default values or computed columns, we are not specifying _how\* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="83b9c-128">I provider di database possono impostare automaticamente la generazione di valori per alcuni tipi di proprietà, ma altri potrebbero richiedere di configurare manualmente la modalità di generazione del valore.</span><span class="sxs-lookup"><span data-stu-id="83b9c-128">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="83b9c-129">Ad esempio, in SQL Server, quando una proprietà GUID viene configurata come valore generato su Aggiungi, il provider esegue automaticamente il lato client di generazione del valore, usando un algoritmo per generare valori GUID sequenziali ottimali.</span><span class="sxs-lookup"><span data-stu-id="83b9c-129">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="83b9c-130">Tuttavia, `ValueGeneratedOnAdd()` l'impostazione di su una proprietà DateTime non avrà alcun effetto ([vedere la sezione seguente per la generazione di valori DateTime](#datetime-value-generation)).</span><span class="sxs-lookup"><span data-stu-id="83b9c-130">However, specifying `ValueGeneratedOnAdd()` on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="83b9c-131">Analogamente, le proprietà byte [] configurate come generate in Aggiungi o aggiorna e contrassegnate come token di concorrenza vengono impostate con il tipo di dati rowversion, in modo che i valori vengano generati automaticamente nel database.</span><span class="sxs-lookup"><span data-stu-id="83b9c-131">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="83b9c-132">Tuttavia, la specifica di `ValueGeneratedOnAddOrUpdate()` non avrà alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="83b9c-132">However, specifying `ValueGeneratedOnAddOrUpdate()` will again have no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="83b9c-133">A seconda del provider di database in uso, è possibile che i valori vengano generati lato client da EF o nel database.</span><span class="sxs-lookup"><span data-stu-id="83b9c-133">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="83b9c-134">Se il valore viene generato dal database, EF può assegnare un valore temporaneo quando si aggiunge l'entità al contesto; Questo valore temporaneo verrà quindi sostituito dal valore generato dal database durante `SaveChanges()` .</span><span class="sxs-lookup"><span data-stu-id="83b9c-134">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="83b9c-135">Per ulteriori informazioni, [vedere la documentazione relativa ai valori temporanei](xref:core/change-tracking/explicit-tracking#temporary-values).</span><span class="sxs-lookup"><span data-stu-id="83b9c-135">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="83b9c-136">Generazione di valori di data/ora</span><span class="sxs-lookup"><span data-stu-id="83b9c-136">Date/time value generation</span></span>

<span data-ttu-id="83b9c-137">Una richiesta comune consiste nel disporre di una colonna di database contenente la data e l'ora in cui la colonna è stata inserita per la prima volta (valore generato al momento dell'aggiunta) o per l'ultimo aggiornamento (valore generato durante l'aggiunta o l'aggiornamento).</span><span class="sxs-lookup"><span data-stu-id="83b9c-137">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="83b9c-138">Poiché esistono diverse strategie per eseguire questa operazione, i provider EF Core in genere non configurano automaticamente la generazione di valori per le colonne di data/ora. è necessario configurarlo manualmente.</span><span class="sxs-lookup"><span data-stu-id="83b9c-138">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="83b9c-139">Timestamp di creazione</span><span class="sxs-lookup"><span data-stu-id="83b9c-139">Creation timestamp</span></span>

<span data-ttu-id="83b9c-140">La configurazione di una colonna di data/ora in modo che il timestamp di creazione della riga riporti in genere la configurazione di un valore predefinito con la funzione SQL appropriata.</span><span class="sxs-lookup"><span data-stu-id="83b9c-140">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="83b9c-141">In SQL Server, ad esempio, è possibile utilizzare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="83b9c-141">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="83b9c-142">Assicurarsi di selezionare la funzione appropriata, in quanto possono esistere diverse (ad esempio, `GETDATE()` vs. `GETUTCDATE()` ).</span><span class="sxs-lookup"><span data-stu-id="83b9c-142">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="83b9c-143">Aggiorna timestamp</span><span class="sxs-lookup"><span data-stu-id="83b9c-143">Update timestamp</span></span>

<span data-ttu-id="83b9c-144">Sebbene le colonne calcolate archiviate sembrino una soluzione efficace per la gestione dei timestamp aggiornati per ultimi, i database in genere non consentono di specificare funzioni come `GETDATE()` in una colonna calcolata.</span><span class="sxs-lookup"><span data-stu-id="83b9c-144">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="83b9c-145">In alternativa, è possibile configurare un trigger di database per ottenere lo stesso effetto:</span><span class="sxs-lookup"><span data-stu-id="83b9c-145">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

<span data-ttu-id="83b9c-146">Per informazioni sulla creazione di trigger, [vedere la documentazione sull'uso di SQL non elaborato nelle migrazioni](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span><span class="sxs-lookup"><span data-stu-id="83b9c-146">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="83b9c-147">Override della generazione di valori</span><span class="sxs-lookup"><span data-stu-id="83b9c-147">Overriding value generation</span></span>

<span data-ttu-id="83b9c-148">Sebbene una proprietà sia configurata per la generazione di valori, in molti casi è comunque possibile specificare in modo esplicito un valore.</span><span class="sxs-lookup"><span data-stu-id="83b9c-148">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="83b9c-149">Il funzionamento di questa funzione dipende dal meccanismo di generazione di valori specifico configurato; Sebbene sia possibile specificare un valore esplicito anziché usare il valore predefinito di una colonna, non è possibile eseguire la stessa operazione con le colonne calcolate.</span><span class="sxs-lookup"><span data-stu-id="83b9c-149">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="83b9c-150">Per eseguire l'override della generazione di valori con un valore esplicito, è sufficiente impostare la proprietà su un valore diverso da quello predefinito di CLR per il tipo di tale proprietà (per, per, `null` `string` per e `0` `int` `Guid.Empty` `Guid` così via).</span><span class="sxs-lookup"><span data-stu-id="83b9c-150">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="83b9c-151">Il tentativo di inserire valori espliciti in SQL Server identità ha esito negativo per impostazione predefinita. [vedere questi documenti per una soluzione alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span><span class="sxs-lookup"><span data-stu-id="83b9c-151">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="83b9c-152">Per fornire un valore esplicito per le proprietà che sono state configurate come valore generato durante l'aggiunta o l'aggiornamento, è necessario configurare anche la proprietà come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="83b9c-152">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="83b9c-153">Nessuna generazione di valori</span><span class="sxs-lookup"><span data-stu-id="83b9c-153">No value generation</span></span>

<span data-ttu-id="83b9c-154">Oltre a scenari specifici come quelli descritti in precedenza, le proprietà non hanno in genere alcuna generazione di valori configurata. Ciò significa che l'applicazione deve sempre fornire un valore da salvare nel database.</span><span class="sxs-lookup"><span data-stu-id="83b9c-154">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="83b9c-155">Questo valore deve essere assegnato a nuove entità prima che vengano aggiunte al contesto.</span><span class="sxs-lookup"><span data-stu-id="83b9c-155">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="83b9c-156">In alcuni casi, tuttavia, potrebbe essere necessario disabilitare la generazione di valori configurata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="83b9c-156">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="83b9c-157">Una chiave primaria di tipo int, ad esempio, viene in genere configurata in modo implicito come valore generato in base al valore aggiunto (ad esempio, colonna Identity in SQL Server).</span><span class="sxs-lookup"><span data-stu-id="83b9c-157">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="83b9c-158">Questa operazione può essere disabilitata tramite quanto segue:</span><span class="sxs-lookup"><span data-stu-id="83b9c-158">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="83b9c-159">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="83b9c-159">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="83b9c-160">API Fluent</span><span class="sxs-lookup"><span data-stu-id="83b9c-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
