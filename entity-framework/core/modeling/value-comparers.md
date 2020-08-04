---
title: Operatori di confronto del valore-EF Core
description: Utilizzo degli operatori di confronto dei valori per controllare la modalità di confronto dei valori delle proprietà EF Core
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526784"
---
# <a name="value-comparers"></a>Operatori di confronto del valore

> [!NOTE]  
> Questa funzionalità è una novità di EF Core 3,0.

> [!TIP]  
> Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Background

EF Core necessario confrontare i valori delle proprietà nei casi seguenti:

* Determinare se una proprietà è stata modificata come parte del [rilevamento delle modifiche per gli aggiornamenti](xref:core/saving/basic)
* Determinare se due valori di chiave sono uguali durante la risoluzione delle relazioni 

Questa operazione viene gestita automaticamente per i tipi primitivi comuni, ad esempio int, bool, DateTime e così via.

Per i tipi più complessi, è necessario effettuare scelte per eseguire il confronto.
Una matrice di byte, ad esempio, può essere confrontata:

* Per riferimento, in modo che venga rilevata una differenza solo se viene utilizzata una nuova matrice di byte
* In base a un confronto approfondito, tale mutazione dei byte nella matrice viene rilevata

Per impostazione predefinita, EF Core usa il primo di questi approcci per le matrici di byte non chiave.
Ovvero, vengono confrontati solo i riferimenti e viene rilevata una modifica solo quando una matrice di byte esistente viene sostituita con una nuova.
Si tratta di una decisione pragmatica che evita il confronto profondo tra molte matrici di byte di grandi dimensioni durante l'esecuzione di SaveChanges.
Tuttavia, lo scenario comune di sostituzione, ad dire, di un'immagine con un'immagine diversa viene gestito in modo efficiente.

D'altra parte, l'uguaglianza dei riferimenti non funzionerà quando le matrici di byte vengono usate per rappresentare le chiavi binarie.
È molto improbabile che una proprietà FK sia impostata sulla _stessa istanza_ di una proprietà PK alla quale deve essere confrontata.
EF Core usa quindi confronti profondi per le matrici di byte che fungono da chiavi.
È improbabile che si sia verificato un notevole calo delle prestazioni poiché le chiavi binarie sono in genere brevi.

### <a name="snapshots"></a>Snapshot

I confronti profondi sui tipi modificabili implicano che EF Core necessita della possibilità di creare uno "snapshot" approfondito del valore della proprietà.
Semplicemente la copia del riferimento comporterebbe la modifica del valore corrente e dello snapshot, poiché si tratta _dello stesso oggetto_.
Pertanto, quando vengono utilizzati confronti profondi sui tipi modificabili, è necessario anche Deep istantanee.

## <a name="properties-with-value-converters"></a>Proprietà con convertitori di valori

Nel caso precedente, EF Core dispone del supporto per il mapping nativo per le matrici di byte e pertanto può scegliere automaticamente le impostazioni predefinite appropriate.
Tuttavia, se la proprietà viene mappata tramite un [convertitore di valori](xref:core/modeling/value-conversions), EF Core non è sempre in grado di determinare il confronto appropriato da usare.
Al contrario, EF Core utilizza sempre il confronto di uguaglianza predefinito definito dal tipo della proprietà.
Questa operazione è spesso corretta, ma potrebbe essere necessario eseguirne l'override quando si esegue il mapping di tipi più complessi.

### <a name="simple-immutable-classes"></a>Classi non modificabili semplici

Si consideri una proprietà che usa un convertitore di valori per eseguire il mapping di una classe semplice e non modificabile.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Per le proprietà di questo tipo non sono necessari confronti o snapshot speciali perché:
* L'uguaglianza viene sottoposta a override in modo da confrontare correttamente le istanze diverse
* Il tipo non è modificabile, quindi non è possibile modificare un valore di snapshot

Quindi, in questo caso, il comportamento predefinito di EF Core è altrettanto appropriato.

### <a name="simple-immutable-structs"></a>Struct non modificabili semplici

Anche il mapping per gli struct semplici è semplice e non richiede alcun operatore di confronto o istantanee speciale.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core dispone del supporto incorporato per la generazione di confronti membro per membro compilati delle proprietà dello struct.
Questo significa che gli struct non devono avere l'override dell'uguaglianza per EF, ma è comunque possibile scegliere di eseguire questa operazione per [altri motivi](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
Inoltre, non è necessario un istantanee speciale perché gli struct non sono modificabili e vengono sempre copiati membro per membro comunque.
Questo vale anche per gli struct modificabili, ma [in generale è consigliabile evitare gli struct modificabili](/dotnet/csharp/write-safe-efficient-code).

### <a name="mutable-classes"></a>Classi modificabili

Quando possibile, è consigliabile utilizzare tipi non modificabili (classi o struct) con convertitori di valori.
Questa operazione è in genere più efficiente e presenta una semantica più pulita rispetto all'uso di un tipo modificabile.

Tuttavia, in questo caso, è normale usare le proprietà dei tipi che l'applicazione non può modificare.
Ad esempio, il mapping di una proprietà contenente un elenco di numeri: 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La [ `List<T>` classe](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):
* Con uguaglianza di riferimento; due elenchi contenenti gli stessi valori vengono considerati diversi.
* È modificabile; i valori nell'elenco possono essere aggiunti e rimossi.

Una conversione tipica di un valore in una proprietà di elenco può convertire l'elenco in e da JSON:

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

È quindi necessario impostare un oggetto `ValueComparer<T>` sulla proprietà per forzare EF Core usare i confronti corretti con questa conversione:

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> L'API del generatore di modelli ("Fluent") per impostare un operatore di confronto dei valori non è stata ancora implementata.
> Al contrario, il codice precedente chiama SetValueComparer nel IMutableProperty di livello inferiore esposto dal generatore come ' Metadata '.

Il `ValueComparer<T>` costruttore accetta tre espressioni:
* Espressione per la verifica dell'uguaglianza
* Espressione per la generazione di un codice hash
* Espressione per lo snapshot di un valore  

In questo caso, il confronto viene eseguito controllando se le sequenze di numeri sono uguali.

Analogamente, il codice hash viene creato dalla stessa sequenza.
Si noti che si tratta di un codice hash rispetto ai valori modificabili e che può [causare problemi](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
Se possibile, non è possibile modificarlo.

Lo snapshot viene creato clonando l'elenco con ToList.
Anche in questo caso, questa operazione è necessaria solo se gli elenchi verranno mutati.
In alternativa, se possibile, non è possibile modificarlo. 

> [!NOTE]  
> I convertitori di valori e gli operatori di confronto vengono costruiti utilizzando espressioni anziché delegati semplici.
> Questo perché EF inserisce queste espressioni in un albero delle espressioni molto più complesso che viene quindi compilato in un delegato di Entity shaper.
> Concettualmente, questo è simile all'incorporamento del compilatore.
> Ad esempio, una conversione semplice può essere solo una compilazione compilata nel cast, anziché una chiamata a un altro metodo per eseguire la conversione.    

### <a name="key-comparers"></a>Operatori di confronto principali

La sezione background illustra il motivo per cui i confronti chiave possono richiedere una semantica speciale.
Assicurarsi di creare un operatore di confronto appropriato per le chiavi quando lo si imposta su una proprietà primaria, principale o di chiave esterna.

Usare [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) nei rari casi in cui è richiesta una semantica diversa per la stessa proprietà.

> [!NOTE]  
> SetStructuralComparer è obsoleto in EF Core 5,0.
> In alternativa, usare SetKeyValueComparer.

### <a name="overriding-defaults"></a>Override dei valori predefiniti

In alcuni casi il confronto predefinito usato da EF Core potrebbe non essere appropriato.
La mutazione di matrici di byte, ad esempio, non è rilevata per impostazione predefinita in EF Core.
È possibile eseguirne l'override impostando un operatore di confronto diverso per la proprietà: 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core ora confronterà le sequenze di byte e rileverà pertanto le mutazioni della matrice di byte.
