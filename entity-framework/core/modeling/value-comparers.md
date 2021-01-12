---
title: Operatori di confronto del valore-EF Core
description: Utilizzo degli operatori di confronto dei valori per controllare la modalità di confronto dei valori delle proprietà EF Core
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 618341315de05f6efae8f43384809ed72226e18b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128511"
---
# <a name="value-comparers"></a><span data-ttu-id="f6528-103">Operatori di confronto del valore</span><span class="sxs-lookup"><span data-stu-id="f6528-103">Value Comparers</span></span>

> [!NOTE]
> <span data-ttu-id="f6528-104">Questa funzionalità è stata introdotta in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f6528-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]
> <span data-ttu-id="f6528-105">Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="f6528-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="f6528-106">Background</span><span class="sxs-lookup"><span data-stu-id="f6528-106">Background</span></span>

<span data-ttu-id="f6528-107">Il rilevamento delle modifiche significa che EF Core determina automaticamente quali modifiche sono state eseguite dall'applicazione in un'istanza di entità caricata, in modo che tali modifiche possano essere salvate nel database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="f6528-107">Change tracking means that EF Core automatically determines what changes were performed by the application on a loaded entity instance, so that those changes can be saved back to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="f6528-108">EF Core in genere esegue questa operazione eseguendo uno *snapshot* dell'istanza quando viene caricata dal database e *confrontando* tale snapshot con l'istanza passata all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f6528-108">EF Core usually performs this by taking a *snapshot* of the instance when it's loaded from the database, and *comparing* that snapshot to the instance handed out to the application.</span></span>

<span data-ttu-id="f6528-109">EF Core viene fornita con la logica incorporata per istantanee e confrontando la maggior parte dei tipi standard utilizzati nei database di, in modo che gli utenti non debbano in genere preoccuparsi di questo argomento.</span><span class="sxs-lookup"><span data-stu-id="f6528-109">EF Core comes with built-in logic for snapshotting and comparing most standard types used in databases, so users don't usually need to worry about this topic.</span></span> <span data-ttu-id="f6528-110">Tuttavia, quando viene eseguito il mapping di una proprietà tramite un [convertitore di valori](xref:core/modeling/value-conversions), EF Core necessario eseguire il confronto su tipi di utente arbitrari, che possono essere complessi.</span><span class="sxs-lookup"><span data-stu-id="f6528-110">However, when a property is mapped through a [value converter](xref:core/modeling/value-conversions), EF Core needs to perform comparison on arbitrary user types, which may be complex.</span></span> <span data-ttu-id="f6528-111">Per impostazione predefinita, EF Core utilizza il confronto di uguaglianza predefinito definito dai tipi (ad esempio, il `Equals` metodo). per istantanee, i tipi di valore vengono copiati per produrre lo snapshot, mentre per i tipi di riferimento non viene eseguita la copia e la stessa istanza viene utilizzata come snapshot.</span><span class="sxs-lookup"><span data-stu-id="f6528-111">By default, EF Core uses the default equality comparison defined by types (e.g. the `Equals` method); for snapshotting, value types are copied to produce the snapshot, while for reference types no copying occurs, and the same instance is used as the snapshot.</span></span>

<span data-ttu-id="f6528-112">Nei casi in cui il comportamento di confronto predefinito non è appropriato, gli utenti possono fornire un *operatore di confronto del valore*, che contiene la logica per istantanee, il confronto e il calcolo di un codice hash.</span><span class="sxs-lookup"><span data-stu-id="f6528-112">In cases where the built-in comparison behavior isn't appropriate, users may provide a *value comparer*, which contains logic for snapshotting, comparing and calculating a hash code.</span></span> <span data-ttu-id="f6528-113">Il codice seguente, ad esempio, imposta la conversione del valore per la proprietà in modo da `List<int>` essere convertita in una stringa JSON nel database e definisce anche un operatore di confronto del valore appropriato:</span><span class="sxs-lookup"><span data-stu-id="f6528-113">For example, the following sets up value conversion for `List<int>` property to be value converted to a JSON string in the database, and defines an appropriate value comparer as well:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="f6528-114">Per ulteriori informazioni, vedere [classi modificabili](#mutable-classes) di seguito.</span><span class="sxs-lookup"><span data-stu-id="f6528-114">See [mutable classes](#mutable-classes) below for further details.</span></span>

<span data-ttu-id="f6528-115">Si noti che gli operatori di confronto dei valori vengono usati anche per determinare se due valori di chiave sono uguali durante la risoluzione delle relazioni; Questa procedura è illustrata di seguito.</span><span class="sxs-lookup"><span data-stu-id="f6528-115">Note that value comparers are also used when determining whether two key values are the same when resolving relationships; this is explained below.</span></span>

## <a name="shallow-vs-deep-comparison"></a><span data-ttu-id="f6528-116">Confronto superficiale e confronto profondo</span><span class="sxs-lookup"><span data-stu-id="f6528-116">Shallow vs. deep comparison</span></span>

<span data-ttu-id="f6528-117">Per i tipi di valore piccoli e non modificabili, ad esempio `int` , la logica predefinita di EF Core funziona bene: il valore viene copiato così com'è quando snapshotted e viene confrontato con il confronto di uguaglianza incorporato del tipo.</span><span class="sxs-lookup"><span data-stu-id="f6528-117">For small, immutable value types such as `int`, EF Core's default logic works well: the value is copied as-is when snapshotted, and compared with the type's built-in equality comparison.</span></span> <span data-ttu-id="f6528-118">Quando si implementa un operatore di confronto dei valori, è importante considerare se la logica di confronto (e istantanee) è appropriata.</span><span class="sxs-lookup"><span data-stu-id="f6528-118">When implementing your own value comparer, it's important to consider whether deep or shallow comparison (and snapshotting) logic is appropriate.</span></span>

<span data-ttu-id="f6528-119">Prendere in considerazione le matrici di byte, che possono essere arbitrariamente grandi.</span><span class="sxs-lookup"><span data-stu-id="f6528-119">Consider byte arrays, which can be arbitrarily large.</span></span> <span data-ttu-id="f6528-120">Questi possono essere confrontati:</span><span class="sxs-lookup"><span data-stu-id="f6528-120">These could be compared:</span></span>

* <span data-ttu-id="f6528-121">Per riferimento, in modo che venga rilevata una differenza solo se viene utilizzata una nuova matrice di byte</span><span class="sxs-lookup"><span data-stu-id="f6528-121">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="f6528-122">In base a un confronto approfondito, tale mutazione dei byte nella matrice viene rilevata</span><span class="sxs-lookup"><span data-stu-id="f6528-122">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="f6528-123">Per impostazione predefinita, EF Core usa il primo di questi approcci per le matrici di byte non chiave.</span><span class="sxs-lookup"><span data-stu-id="f6528-123">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span> <span data-ttu-id="f6528-124">Ovvero, vengono confrontati solo i riferimenti e viene rilevata una modifica solo quando una matrice di byte esistente viene sostituita con una nuova.</span><span class="sxs-lookup"><span data-stu-id="f6528-124">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span> <span data-ttu-id="f6528-125">Si tratta di una decisione pragmatica che evita la copia di intere matrici e il confronto di byte a byte durante <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> l'esecuzione e lo scenario comune di sostituzione, ad dire, di un'immagine con un'altra viene gestita in modo efficiente.</span><span class="sxs-lookup"><span data-stu-id="f6528-125">This is a pragmatic decision that avoids copying entire arrays and comparing them byte-to-byte when executing <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, and the common scenario of replacing, say, one image with another is handled in a performant way.</span></span>

<span data-ttu-id="f6528-126">D'altra parte, l'uguaglianza dei riferimenti non funzionerà quando le matrici di byte vengono usate per rappresentare le chiavi binarie, perché è molto improbabile che una proprietà FK sia impostata sulla _stessa istanza_ di una proprietà PK alla quale deve essere confrontata.</span><span class="sxs-lookup"><span data-stu-id="f6528-126">On the other hand, reference equality would not work when byte arrays are used to represent binary keys, since it's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span> <span data-ttu-id="f6528-127">EF Core usa quindi confronti profondi per le matrici di byte che fungono da chiavi; è improbabile che si sia verificato un notevole calo delle prestazioni poiché le chiavi binarie sono in genere brevi.</span><span class="sxs-lookup"><span data-stu-id="f6528-127">Therefore, EF Core uses deep comparisons for byte arrays acting as keys; this is unlikely to have a big performance hit since binary keys are usually short.</span></span>

<span data-ttu-id="f6528-128">Si noti che il confronto e la logica istantanee scelti devono corrispondere tra loro: il confronto profondo richiede un istantanee approfondito per il corretto funzionamento.</span><span class="sxs-lookup"><span data-stu-id="f6528-128">Note that the chosen comparison and snapshotting logic must correspond to each other: deep comparison requires deep snapshotting to function correctly.</span></span>

## <a name="simple-immutable-classes"></a><span data-ttu-id="f6528-129">Classi non modificabili semplici</span><span class="sxs-lookup"><span data-stu-id="f6528-129">Simple immutable classes</span></span>

<span data-ttu-id="f6528-130">Si consideri una proprietà che usa un convertitore di valori per eseguire il mapping di una classe semplice e non modificabile.</span><span class="sxs-lookup"><span data-stu-id="f6528-130">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="f6528-131">Per le proprietà di questo tipo non sono necessari confronti o snapshot speciali perché:</span><span class="sxs-lookup"><span data-stu-id="f6528-131">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="f6528-132">L'uguaglianza viene sottoposta a override in modo da confrontare correttamente le istanze diverse</span><span class="sxs-lookup"><span data-stu-id="f6528-132">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="f6528-133">Il tipo non è modificabile, quindi non è possibile modificare un valore di snapshot</span><span class="sxs-lookup"><span data-stu-id="f6528-133">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="f6528-134">Quindi, in questo caso, il comportamento predefinito di EF Core è altrettanto appropriato.</span><span class="sxs-lookup"><span data-stu-id="f6528-134">So in this case the default behavior of EF Core is fine as it is.</span></span>

## <a name="simple-immutable-structs"></a><span data-ttu-id="f6528-135">Struct non modificabili semplici</span><span class="sxs-lookup"><span data-stu-id="f6528-135">Simple immutable structs</span></span>

<span data-ttu-id="f6528-136">Anche il mapping per gli struct semplici è semplice e non richiede alcun operatore di confronto o istantanee speciale.</span><span class="sxs-lookup"><span data-stu-id="f6528-136">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="f6528-137">EF Core dispone del supporto incorporato per la generazione di confronti membro per membro compilati delle proprietà dello struct.</span><span class="sxs-lookup"><span data-stu-id="f6528-137">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="f6528-138">Questo significa che gli struct non devono avere l'override dell'uguaglianza per EF Core, ma è comunque possibile scegliere di eseguire questa operazione per [altri motivi](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="f6528-138">This means structs don't need to have equality overridden for EF Core, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="f6528-139">Inoltre, istantanee speciali non è necessario perché gli struct non sono modificabili e vengono sempre copiati membro per membro comunque.</span><span class="sxs-lookup"><span data-stu-id="f6528-139">Also, special snapshotting is not needed since structs are immutable and are always copied memberwise anyway.</span></span>
<span data-ttu-id="f6528-140">Questo vale anche per gli struct modificabili, ma [in generale è consigliabile evitare gli struct modificabili](/dotnet/csharp/write-safe-efficient-code).</span><span class="sxs-lookup"><span data-stu-id="f6528-140">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

## <a name="mutable-classes"></a><span data-ttu-id="f6528-141">Classi modificabili</span><span class="sxs-lookup"><span data-stu-id="f6528-141">Mutable classes</span></span>

<span data-ttu-id="f6528-142">Quando possibile, è consigliabile utilizzare tipi non modificabili (classi o struct) con convertitori di valori.</span><span class="sxs-lookup"><span data-stu-id="f6528-142">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="f6528-143">Questa operazione è in genere più efficiente e presenta una semantica più pulita rispetto all'uso di un tipo modificabile.</span><span class="sxs-lookup"><span data-stu-id="f6528-143">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="f6528-144">Tuttavia, in questo caso, è normale usare le proprietà dei tipi che l'applicazione non può modificare.</span><span class="sxs-lookup"><span data-stu-id="f6528-144">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="f6528-145">Ad esempio, il mapping di una proprietà contenente un elenco di numeri:</span><span class="sxs-lookup"><span data-stu-id="f6528-145">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="f6528-146">La classe <xref:System.Collections.Generic.List%601>:</span><span class="sxs-lookup"><span data-stu-id="f6528-146">The <xref:System.Collections.Generic.List%601> class:</span></span>

* <span data-ttu-id="f6528-147">Con uguaglianza di riferimento; due elenchi contenenti gli stessi valori vengono considerati diversi.</span><span class="sxs-lookup"><span data-stu-id="f6528-147">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="f6528-148">È modificabile; i valori nell'elenco possono essere aggiunti e rimossi.</span><span class="sxs-lookup"><span data-stu-id="f6528-148">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="f6528-149">Una conversione tipica di un valore in una proprietà di elenco può convertire l'elenco in e da JSON:</span><span class="sxs-lookup"><span data-stu-id="f6528-149">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

### <a name="ef-core-50"></a>[<span data-ttu-id="f6528-150">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="f6528-150">EF Core 5.0</span></span>](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[<span data-ttu-id="f6528-151">Versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="f6528-151">Older versions</span></span>](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

<span data-ttu-id="f6528-152">Il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> costruttore accetta tre espressioni:</span><span class="sxs-lookup"><span data-stu-id="f6528-152">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor accepts three expressions:</span></span>

* <span data-ttu-id="f6528-153">Espressione per la verifica dell'uguaglianza</span><span class="sxs-lookup"><span data-stu-id="f6528-153">An expression for checking equality</span></span>
* <span data-ttu-id="f6528-154">Espressione per la generazione di un codice hash</span><span class="sxs-lookup"><span data-stu-id="f6528-154">An expression for generating a hash code</span></span>
* <span data-ttu-id="f6528-155">Espressione per lo snapshot di un valore</span><span class="sxs-lookup"><span data-stu-id="f6528-155">An expression to snapshot a value</span></span>

<span data-ttu-id="f6528-156">In questo caso, il confronto viene eseguito controllando se le sequenze di numeri sono uguali.</span><span class="sxs-lookup"><span data-stu-id="f6528-156">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="f6528-157">Analogamente, il codice hash viene creato dalla stessa sequenza.</span><span class="sxs-lookup"><span data-stu-id="f6528-157">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="f6528-158">Si noti che si tratta di un codice hash rispetto ai valori modificabili e che può [causare problemi](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="f6528-158">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="f6528-159">Se possibile, non è possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="f6528-159">Be immutable instead if you can.)</span></span>

<span data-ttu-id="f6528-160">Lo snapshot viene creato clonando l'elenco con `ToList` .</span><span class="sxs-lookup"><span data-stu-id="f6528-160">The snapshot is created by cloning the list with `ToList`.</span></span>
<span data-ttu-id="f6528-161">Anche in questo caso, questa operazione è necessaria solo se gli elenchi verranno mutati.</span><span class="sxs-lookup"><span data-stu-id="f6528-161">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="f6528-162">In alternativa, se possibile, non è possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="f6528-162">Be immutable instead if you can.</span></span>

> [!NOTE]
> <span data-ttu-id="f6528-163">I convertitori di valori e gli operatori di confronto vengono costruiti utilizzando espressioni anziché delegati semplici.</span><span class="sxs-lookup"><span data-stu-id="f6528-163">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="f6528-164">Questo perché EF Core inserisce queste espressioni in un albero delle espressioni molto più complesso che viene quindi compilato in un delegato di Entity shaper.</span><span class="sxs-lookup"><span data-stu-id="f6528-164">This is because EF Core inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="f6528-165">Concettualmente, questo è simile all'incorporamento del compilatore.</span><span class="sxs-lookup"><span data-stu-id="f6528-165">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="f6528-166">Ad esempio, una conversione semplice può essere solo una compilazione compilata nel cast, anziché una chiamata a un altro metodo per eseguire la conversione.</span><span class="sxs-lookup"><span data-stu-id="f6528-166">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

## <a name="key-comparers"></a><span data-ttu-id="f6528-167">Operatori di confronto principali</span><span class="sxs-lookup"><span data-stu-id="f6528-167">Key comparers</span></span>

<span data-ttu-id="f6528-168">La sezione background illustra il motivo per cui i confronti chiave possono richiedere una semantica speciale.</span><span class="sxs-lookup"><span data-stu-id="f6528-168">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="f6528-169">Assicurarsi di creare un operatore di confronto appropriato per le chiavi quando lo si imposta su una proprietà primaria, principale o di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="f6528-169">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="f6528-170">Usare <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> nei rari casi in cui è richiesta una semantica diversa per la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="f6528-170">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]
> <span data-ttu-id="f6528-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> è stata obsoleta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f6528-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> has been obsoleted in EF Core 5.0.</span></span> <span data-ttu-id="f6528-172">In alternativa, utilizzare <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.</span><span class="sxs-lookup"><span data-stu-id="f6528-172">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> instead.</span></span>

## <a name="overriding-the-default-comparer"></a><span data-ttu-id="f6528-173">Override dell'operatore di confronto predefinito</span><span class="sxs-lookup"><span data-stu-id="f6528-173">Overriding the default comparer</span></span>

<span data-ttu-id="f6528-174">In alcuni casi il confronto predefinito usato da EF Core potrebbe non essere appropriato.</span><span class="sxs-lookup"><span data-stu-id="f6528-174">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="f6528-175">La mutazione di matrici di byte, ad esempio, non è rilevata per impostazione predefinita in EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6528-175">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="f6528-176">È possibile eseguirne l'override impostando un operatore di confronto diverso per la proprietà:</span><span class="sxs-lookup"><span data-stu-id="f6528-176">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="f6528-177">EF Core ora confronterà le sequenze di byte e rileverà pertanto le mutazioni della matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="f6528-177">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
