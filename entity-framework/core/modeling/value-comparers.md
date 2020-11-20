---
title: Operatori di confronto del valore-EF Core
description: Utilizzo degli operatori di confronto dei valori per controllare la modalità di confronto dei valori delle proprietà EF Core
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: d59ab093c7e9d251aac80420972d3e35edabae0b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003614"
---
# <a name="value-comparers"></a><span data-ttu-id="44e0d-103">Operatori di confronto del valore</span><span class="sxs-lookup"><span data-stu-id="44e0d-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="44e0d-104">Questa funzionalità è stata introdotta in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="44e0d-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="44e0d-105">Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="44e0d-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="44e0d-106">Sfondo</span><span class="sxs-lookup"><span data-stu-id="44e0d-106">Background</span></span>

<span data-ttu-id="44e0d-107">EF Core necessario confrontare i valori delle proprietà nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="44e0d-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="44e0d-108">Determinare se una proprietà è stata modificata come parte del [rilevamento delle modifiche per gli aggiornamenti](xref:core/saving/basic)</span><span class="sxs-lookup"><span data-stu-id="44e0d-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="44e0d-109">Determinare se due valori di chiave sono uguali durante la risoluzione delle relazioni</span><span class="sxs-lookup"><span data-stu-id="44e0d-109">Determining whether two key values are the same when resolving relationships</span></span>

<span data-ttu-id="44e0d-110">Questa operazione viene gestita automaticamente per i tipi primitivi comuni, ad esempio int, bool, DateTime e così via.</span><span class="sxs-lookup"><span data-stu-id="44e0d-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="44e0d-111">Per i tipi più complessi, è necessario effettuare scelte per eseguire il confronto.</span><span class="sxs-lookup"><span data-stu-id="44e0d-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="44e0d-112">Una matrice di byte, ad esempio, può essere confrontata:</span><span class="sxs-lookup"><span data-stu-id="44e0d-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="44e0d-113">Per riferimento, in modo che venga rilevata una differenza solo se viene utilizzata una nuova matrice di byte</span><span class="sxs-lookup"><span data-stu-id="44e0d-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="44e0d-114">In base a un confronto approfondito, tale mutazione dei byte nella matrice viene rilevata</span><span class="sxs-lookup"><span data-stu-id="44e0d-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="44e0d-115">Per impostazione predefinita, EF Core usa il primo di questi approcci per le matrici di byte non chiave.</span><span class="sxs-lookup"><span data-stu-id="44e0d-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="44e0d-116">Ovvero, vengono confrontati solo i riferimenti e viene rilevata una modifica solo quando una matrice di byte esistente viene sostituita con una nuova.</span><span class="sxs-lookup"><span data-stu-id="44e0d-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="44e0d-117">Si tratta di una decisione pragmatica che evita il confronto profondo tra molte matrici di byte di grandi dimensioni durante l'esecuzione di SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="44e0d-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="44e0d-118">Tuttavia, lo scenario comune di sostituzione, ad dire, di un'immagine con un'immagine diversa viene gestito in modo efficiente.</span><span class="sxs-lookup"><span data-stu-id="44e0d-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="44e0d-119">D'altra parte, l'uguaglianza dei riferimenti non funzionerà quando le matrici di byte vengono usate per rappresentare le chiavi binarie.</span><span class="sxs-lookup"><span data-stu-id="44e0d-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="44e0d-120">È molto improbabile che una proprietà FK sia impostata sulla _stessa istanza_ di una proprietà PK alla quale deve essere confrontata.</span><span class="sxs-lookup"><span data-stu-id="44e0d-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="44e0d-121">EF Core usa quindi confronti profondi per le matrici di byte che fungono da chiavi.</span><span class="sxs-lookup"><span data-stu-id="44e0d-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="44e0d-122">È improbabile che si sia verificato un notevole calo delle prestazioni poiché le chiavi binarie sono in genere brevi.</span><span class="sxs-lookup"><span data-stu-id="44e0d-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="44e0d-123">Snapshot</span><span class="sxs-lookup"><span data-stu-id="44e0d-123">Snapshots</span></span>

<span data-ttu-id="44e0d-124">I confronti profondi sui tipi modificabili implicano che EF Core necessita della possibilità di creare uno "snapshot" approfondito del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="44e0d-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="44e0d-125">Semplicemente la copia del riferimento comporterebbe la modifica del valore corrente e dello snapshot, poiché si tratta _dello stesso oggetto_.</span><span class="sxs-lookup"><span data-stu-id="44e0d-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="44e0d-126">Pertanto, quando vengono utilizzati confronti profondi sui tipi modificabili, è necessario anche Deep istantanee.</span><span class="sxs-lookup"><span data-stu-id="44e0d-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="44e0d-127">Proprietà con convertitori di valori</span><span class="sxs-lookup"><span data-stu-id="44e0d-127">Properties with value converters</span></span>

<span data-ttu-id="44e0d-128">Nel caso precedente, EF Core dispone del supporto per il mapping nativo per le matrici di byte e pertanto può scegliere automaticamente le impostazioni predefinite appropriate.</span><span class="sxs-lookup"><span data-stu-id="44e0d-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="44e0d-129">Tuttavia, se la proprietà viene mappata tramite un [convertitore di valori](xref:core/modeling/value-conversions), EF Core non è sempre in grado di determinare il confronto appropriato da usare.</span><span class="sxs-lookup"><span data-stu-id="44e0d-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="44e0d-130">Al contrario, EF Core utilizza sempre il confronto di uguaglianza predefinito definito dal tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="44e0d-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="44e0d-131">Questa operazione è spesso corretta, ma potrebbe essere necessario eseguirne l'override quando si esegue il mapping di tipi più complessi.</span><span class="sxs-lookup"><span data-stu-id="44e0d-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="44e0d-132">Classi non modificabili semplici</span><span class="sxs-lookup"><span data-stu-id="44e0d-132">Simple immutable classes</span></span>

<span data-ttu-id="44e0d-133">Si consideri una proprietà che usa un convertitore di valori per eseguire il mapping di una classe semplice e non modificabile.</span><span class="sxs-lookup"><span data-stu-id="44e0d-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="44e0d-134">Per le proprietà di questo tipo non sono necessari confronti o snapshot speciali perché:</span><span class="sxs-lookup"><span data-stu-id="44e0d-134">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="44e0d-135">L'uguaglianza viene sottoposta a override in modo da confrontare correttamente le istanze diverse</span><span class="sxs-lookup"><span data-stu-id="44e0d-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="44e0d-136">Il tipo non è modificabile, quindi non è possibile modificare un valore di snapshot</span><span class="sxs-lookup"><span data-stu-id="44e0d-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="44e0d-137">Quindi, in questo caso, il comportamento predefinito di EF Core è altrettanto appropriato.</span><span class="sxs-lookup"><span data-stu-id="44e0d-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="44e0d-138">Struct non modificabili semplici</span><span class="sxs-lookup"><span data-stu-id="44e0d-138">Simple immutable Structs</span></span>

<span data-ttu-id="44e0d-139">Anche il mapping per gli struct semplici è semplice e non richiede alcun operatore di confronto o istantanee speciale.</span><span class="sxs-lookup"><span data-stu-id="44e0d-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="44e0d-140">EF Core dispone del supporto incorporato per la generazione di confronti membro per membro compilati delle proprietà dello struct.</span><span class="sxs-lookup"><span data-stu-id="44e0d-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="44e0d-141">Questo significa che gli struct non devono avere l'override dell'uguaglianza per EF, ma è comunque possibile scegliere di eseguire questa operazione per [altri motivi](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="44e0d-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="44e0d-142">Inoltre, non è necessario un istantanee speciale perché gli struct non sono modificabili e vengono sempre copiati membro per membro comunque.</span><span class="sxs-lookup"><span data-stu-id="44e0d-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="44e0d-143">Questo vale anche per gli struct modificabili, ma [in generale è consigliabile evitare gli struct modificabili](/dotnet/csharp/write-safe-efficient-code).</span><span class="sxs-lookup"><span data-stu-id="44e0d-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="44e0d-144">Classi modificabili</span><span class="sxs-lookup"><span data-stu-id="44e0d-144">Mutable classes</span></span>

<span data-ttu-id="44e0d-145">Quando possibile, è consigliabile utilizzare tipi non modificabili (classi o struct) con convertitori di valori.</span><span class="sxs-lookup"><span data-stu-id="44e0d-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="44e0d-146">Questa operazione è in genere più efficiente e presenta una semantica più pulita rispetto all'uso di un tipo modificabile.</span><span class="sxs-lookup"><span data-stu-id="44e0d-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="44e0d-147">Tuttavia, in questo caso, è normale usare le proprietà dei tipi che l'applicazione non può modificare.</span><span class="sxs-lookup"><span data-stu-id="44e0d-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="44e0d-148">Ad esempio, il mapping di una proprietà contenente un elenco di numeri:</span><span class="sxs-lookup"><span data-stu-id="44e0d-148">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="44e0d-149">La [ `List<T>` classe](/dotnet/api/system.collections.generic.list-1):</span><span class="sxs-lookup"><span data-stu-id="44e0d-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1):</span></span>

* <span data-ttu-id="44e0d-150">Con uguaglianza di riferimento; due elenchi contenenti gli stessi valori vengono considerati diversi.</span><span class="sxs-lookup"><span data-stu-id="44e0d-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="44e0d-151">È modificabile; i valori nell'elenco possono essere aggiunti e rimossi.</span><span class="sxs-lookup"><span data-stu-id="44e0d-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="44e0d-152">Una conversione tipica di un valore in una proprietà di elenco può convertire l'elenco in e da JSON:</span><span class="sxs-lookup"><span data-stu-id="44e0d-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="44e0d-153">È quindi necessario impostare un oggetto `ValueComparer<T>` sulla proprietà per forzare EF Core usare i confronti corretti con questa conversione:</span><span class="sxs-lookup"><span data-stu-id="44e0d-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="44e0d-154">L'API del generatore di modelli ("Fluent") per impostare un operatore di confronto dei valori non è stata ancora implementata.</span><span class="sxs-lookup"><span data-stu-id="44e0d-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="44e0d-155">Al contrario, il codice precedente chiama SetValueComparer nel IMutableProperty di livello inferiore esposto dal generatore come ' Metadata '.</span><span class="sxs-lookup"><span data-stu-id="44e0d-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="44e0d-156">Il `ValueComparer<T>` costruttore accetta tre espressioni:</span><span class="sxs-lookup"><span data-stu-id="44e0d-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>

* <span data-ttu-id="44e0d-157">Espressione per la verifica dell'uguaglianza</span><span class="sxs-lookup"><span data-stu-id="44e0d-157">An expression for checking equality</span></span>
* <span data-ttu-id="44e0d-158">Espressione per la generazione di un codice hash</span><span class="sxs-lookup"><span data-stu-id="44e0d-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="44e0d-159">Espressione per lo snapshot di un valore</span><span class="sxs-lookup"><span data-stu-id="44e0d-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="44e0d-160">In questo caso, il confronto viene eseguito controllando se le sequenze di numeri sono uguali.</span><span class="sxs-lookup"><span data-stu-id="44e0d-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="44e0d-161">Analogamente, il codice hash viene creato dalla stessa sequenza.</span><span class="sxs-lookup"><span data-stu-id="44e0d-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="44e0d-162">Si noti che si tratta di un codice hash rispetto ai valori modificabili e che può [causare problemi](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="44e0d-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="44e0d-163">Se possibile, non è possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="44e0d-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="44e0d-164">Lo snapshot viene creato clonando l'elenco con ToList.</span><span class="sxs-lookup"><span data-stu-id="44e0d-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="44e0d-165">Anche in questo caso, questa operazione è necessaria solo se gli elenchi verranno mutati.</span><span class="sxs-lookup"><span data-stu-id="44e0d-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="44e0d-166">In alternativa, se possibile, non è possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="44e0d-166">Be immutable instead if you can.</span></span>

> [!NOTE]  
> <span data-ttu-id="44e0d-167">I convertitori di valori e gli operatori di confronto vengono costruiti utilizzando espressioni anziché delegati semplici.</span><span class="sxs-lookup"><span data-stu-id="44e0d-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="44e0d-168">Questo perché EF inserisce queste espressioni in un albero delle espressioni molto più complesso che viene quindi compilato in un delegato di Entity shaper.</span><span class="sxs-lookup"><span data-stu-id="44e0d-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="44e0d-169">Concettualmente, questo è simile all'incorporamento del compilatore.</span><span class="sxs-lookup"><span data-stu-id="44e0d-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="44e0d-170">Ad esempio, una conversione semplice può essere solo una compilazione compilata nel cast, anziché una chiamata a un altro metodo per eseguire la conversione.</span><span class="sxs-lookup"><span data-stu-id="44e0d-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

### <a name="key-comparers"></a><span data-ttu-id="44e0d-171">Operatori di confronto principali</span><span class="sxs-lookup"><span data-stu-id="44e0d-171">Key comparers</span></span>

<span data-ttu-id="44e0d-172">La sezione background illustra il motivo per cui i confronti chiave possono richiedere una semantica speciale.</span><span class="sxs-lookup"><span data-stu-id="44e0d-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="44e0d-173">Assicurarsi di creare un operatore di confronto appropriato per le chiavi quando lo si imposta su una proprietà primaria, principale o di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="44e0d-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="44e0d-174">Usare [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) nei rari casi in cui è richiesta una semantica diversa per la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="44e0d-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="44e0d-175">SetStructuralComparer è obsoleto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="44e0d-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="44e0d-176">In alternativa, usare SetKeyValueComparer.</span><span class="sxs-lookup"><span data-stu-id="44e0d-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="44e0d-177">Override dei valori predefiniti</span><span class="sxs-lookup"><span data-stu-id="44e0d-177">Overriding defaults</span></span>

<span data-ttu-id="44e0d-178">In alcuni casi il confronto predefinito usato da EF Core potrebbe non essere appropriato.</span><span class="sxs-lookup"><span data-stu-id="44e0d-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="44e0d-179">La mutazione di matrici di byte, ad esempio, non è rilevata per impostazione predefinita in EF Core.</span><span class="sxs-lookup"><span data-stu-id="44e0d-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="44e0d-180">È possibile eseguirne l'override impostando un operatore di confronto diverso per la proprietà:</span><span class="sxs-lookup"><span data-stu-id="44e0d-180">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="44e0d-181">EF Core ora confronterà le sequenze di byte e rileverà pertanto le mutazioni della matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="44e0d-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
