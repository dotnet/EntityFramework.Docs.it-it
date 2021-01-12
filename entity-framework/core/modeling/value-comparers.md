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
# <a name="value-comparers"></a>Operatori di confronto del valore

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 3,0.

> [!TIP]
> Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Background

Il rilevamento delle modifiche significa che EF Core determina automaticamente quali modifiche sono state eseguite dall'applicazione in un'istanza di entità caricata, in modo che tali modifiche possano essere salvate nel database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo. EF Core in genere esegue questa operazione eseguendo uno *snapshot* dell'istanza quando viene caricata dal database e *confrontando* tale snapshot con l'istanza passata all'applicazione.

EF Core viene fornita con la logica incorporata per istantanee e confrontando la maggior parte dei tipi standard utilizzati nei database di, in modo che gli utenti non debbano in genere preoccuparsi di questo argomento. Tuttavia, quando viene eseguito il mapping di una proprietà tramite un [convertitore di valori](xref:core/modeling/value-conversions), EF Core necessario eseguire il confronto su tipi di utente arbitrari, che possono essere complessi. Per impostazione predefinita, EF Core utilizza il confronto di uguaglianza predefinito definito dai tipi (ad esempio, il `Equals` metodo). per istantanee, i tipi di valore vengono copiati per produrre lo snapshot, mentre per i tipi di riferimento non viene eseguita la copia e la stessa istanza viene utilizzata come snapshot.

Nei casi in cui il comportamento di confronto predefinito non è appropriato, gli utenti possono fornire un *operatore di confronto del valore*, che contiene la logica per istantanee, il confronto e il calcolo di un codice hash. Il codice seguente, ad esempio, imposta la conversione del valore per la proprietà in modo da `List<int>` essere convertita in una stringa JSON nel database e definisce anche un operatore di confronto del valore appropriato:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Per ulteriori informazioni, vedere [classi modificabili](#mutable-classes) di seguito.

Si noti che gli operatori di confronto dei valori vengono usati anche per determinare se due valori di chiave sono uguali durante la risoluzione delle relazioni; Questa procedura è illustrata di seguito.

## <a name="shallow-vs-deep-comparison"></a>Confronto superficiale e confronto profondo

Per i tipi di valore piccoli e non modificabili, ad esempio `int` , la logica predefinita di EF Core funziona bene: il valore viene copiato così com'è quando snapshotted e viene confrontato con il confronto di uguaglianza incorporato del tipo. Quando si implementa un operatore di confronto dei valori, è importante considerare se la logica di confronto (e istantanee) è appropriata.

Prendere in considerazione le matrici di byte, che possono essere arbitrariamente grandi. Questi possono essere confrontati:

* Per riferimento, in modo che venga rilevata una differenza solo se viene utilizzata una nuova matrice di byte
* In base a un confronto approfondito, tale mutazione dei byte nella matrice viene rilevata

Per impostazione predefinita, EF Core usa il primo di questi approcci per le matrici di byte non chiave. Ovvero, vengono confrontati solo i riferimenti e viene rilevata una modifica solo quando una matrice di byte esistente viene sostituita con una nuova. Si tratta di una decisione pragmatica che evita la copia di intere matrici e il confronto di byte a byte durante <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> l'esecuzione e lo scenario comune di sostituzione, ad dire, di un'immagine con un'altra viene gestita in modo efficiente.

D'altra parte, l'uguaglianza dei riferimenti non funzionerà quando le matrici di byte vengono usate per rappresentare le chiavi binarie, perché è molto improbabile che una proprietà FK sia impostata sulla _stessa istanza_ di una proprietà PK alla quale deve essere confrontata. EF Core usa quindi confronti profondi per le matrici di byte che fungono da chiavi; è improbabile che si sia verificato un notevole calo delle prestazioni poiché le chiavi binarie sono in genere brevi.

Si noti che il confronto e la logica istantanee scelti devono corrispondere tra loro: il confronto profondo richiede un istantanee approfondito per il corretto funzionamento.

## <a name="simple-immutable-classes"></a>Classi non modificabili semplici

Si consideri una proprietà che usa un convertitore di valori per eseguire il mapping di una classe semplice e non modificabile.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Per le proprietà di questo tipo non sono necessari confronti o snapshot speciali perché:

* L'uguaglianza viene sottoposta a override in modo da confrontare correttamente le istanze diverse
* Il tipo non è modificabile, quindi non è possibile modificare un valore di snapshot

Quindi, in questo caso, il comportamento predefinito di EF Core è altrettanto appropriato.

## <a name="simple-immutable-structs"></a>Struct non modificabili semplici

Anche il mapping per gli struct semplici è semplice e non richiede alcun operatore di confronto o istantanee speciale.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core dispone del supporto incorporato per la generazione di confronti membro per membro compilati delle proprietà dello struct.
Questo significa che gli struct non devono avere l'override dell'uguaglianza per EF Core, ma è comunque possibile scegliere di eseguire questa operazione per [altri motivi](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
Inoltre, istantanee speciali non è necessario perché gli struct non sono modificabili e vengono sempre copiati membro per membro comunque.
Questo vale anche per gli struct modificabili, ma [in generale è consigliabile evitare gli struct modificabili](/dotnet/csharp/write-safe-efficient-code).

## <a name="mutable-classes"></a>Classi modificabili

Quando possibile, è consigliabile utilizzare tipi non modificabili (classi o struct) con convertitori di valori.
Questa operazione è in genere più efficiente e presenta una semantica più pulita rispetto all'uso di un tipo modificabile.

Tuttavia, in questo caso, è normale usare le proprietà dei tipi che l'applicazione non può modificare.
Ad esempio, il mapping di una proprietà contenente un elenco di numeri:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La classe <xref:System.Collections.Generic.List%601>:

* Con uguaglianza di riferimento; due elenchi contenenti gli stessi valori vengono considerati diversi.
* È modificabile; i valori nell'elenco possono essere aggiunti e rimossi.

Una conversione tipica di un valore in una proprietà di elenco può convertire l'elenco in e da JSON:

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[Versioni precedenti](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

Il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> costruttore accetta tre espressioni:

* Espressione per la verifica dell'uguaglianza
* Espressione per la generazione di un codice hash
* Espressione per lo snapshot di un valore

In questo caso, il confronto viene eseguito controllando se le sequenze di numeri sono uguali.

Analogamente, il codice hash viene creato dalla stessa sequenza.
Si noti che si tratta di un codice hash rispetto ai valori modificabili e che può [causare problemi](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
Se possibile, non è possibile modificarlo.

Lo snapshot viene creato clonando l'elenco con `ToList` .
Anche in questo caso, questa operazione è necessaria solo se gli elenchi verranno mutati.
In alternativa, se possibile, non è possibile modificarlo.

> [!NOTE]
> I convertitori di valori e gli operatori di confronto vengono costruiti utilizzando espressioni anziché delegati semplici.
> Questo perché EF Core inserisce queste espressioni in un albero delle espressioni molto più complesso che viene quindi compilato in un delegato di Entity shaper.
> Concettualmente, questo è simile all'incorporamento del compilatore.
> Ad esempio, una conversione semplice può essere solo una compilazione compilata nel cast, anziché una chiamata a un altro metodo per eseguire la conversione.

## <a name="key-comparers"></a>Operatori di confronto principali

La sezione background illustra il motivo per cui i confronti chiave possono richiedere una semantica speciale.
Assicurarsi di creare un operatore di confronto appropriato per le chiavi quando lo si imposta su una proprietà primaria, principale o di chiave esterna.

Usare <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> nei rari casi in cui è richiesta una semantica diversa per la stessa proprietà.

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> è stata obsoleta in EF Core 5,0. In alternativa, utilizzare <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.

## <a name="overriding-the-default-comparer"></a>Override dell'operatore di confronto predefinito

In alcuni casi il confronto predefinito usato da EF Core potrebbe non essere appropriato.
La mutazione di matrici di byte, ad esempio, non è rilevata per impostazione predefinita in EF Core.
È possibile eseguirne l'override impostando un operatore di confronto diverso per la proprietà:

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core ora confronterà le sequenze di byte e rileverà pertanto le mutazioni della matrice di byte.
