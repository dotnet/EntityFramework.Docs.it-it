---
title: Utilizzo dei tipi di riferimento Nullable-EF Core
description: Utilizzo di tipi di riferimento Nullable C# quando si utilizza Entity Framework Core
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 648b79576838d2ba424b5216d5ad6811912f8ccb
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429715"
---
# <a name="working-with-nullable-reference-types"></a>Utilizzo dei tipi di riferimento Nullable

In C# 8 è stata introdotta una nuova funzionalità denominata [tipi di riferimento Nullable (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), che consente di aggiungere annotazioni ai tipi di riferimento, indicando se è valido per consentirne la presenza di valori null. Se non si ha familiarità con questa funzionalità, è consigliabile prenderne nota leggendo i documenti in C#.

Questa pagina introduce il supporto di EF Core per i tipi di riferimento nullable e descrive le procedure consigliate per l'utilizzo.

## <a name="required-and-optional-properties"></a>Proprietà obbligatorie e facoltative

La documentazione principale sulle proprietà obbligatorie e facoltative e la relativa interazione con i tipi di riferimento Nullable è la pagina delle [proprietà obbligatoria e facoltativa](xref:core/modeling/entity-properties#required-and-optional-properties) . È consigliabile iniziare leggendo prima di tutto la pagina.

> [!NOTE]
> Prestare attenzione quando si abilitano i tipi di riferimento nullable in un progetto esistente: le proprietà del tipo di riferimento che in precedenza erano configurate come facoltative ora verranno configurate come richieste, a meno che non vengano annotate in modo esplicito come Nullable. Quando si gestisce uno schema di database relazionale, è possibile che vengano generate migrazioni che modificano il supporto di valori null per la colonna di database.

## <a name="non-nullable-properties-and-initialization"></a>Proprietà e inizializzazione non nullable

Quando sono abilitati i tipi di riferimento Nullable, il compilatore C# genera avvisi per qualsiasi proprietà non nullable non inizializzata, in quanto questi contengono valori null. Di conseguenza, non è possibile usare il modo comune seguente per scrivere i tipi di entità:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=4-5)]

Il [binding del costruttore](xref:core/modeling/constructors) è una tecnica utile per garantire che le proprietà non nullable siano inizializzate:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

Sfortunatamente, in alcuni scenari il binding del costruttore non è un'opzione. le proprietà di navigazione, ad esempio, non possono essere inizializzate in questo modo.

Le proprietà di navigazione obbligatorie presentano un ulteriore problema: Sebbene un dipendente esista sempre per un'entità specifica, può essere caricato o meno da una determinata query, a seconda delle esigenze in quel punto del programma ([vedere i diversi modelli per il caricamento dei dati](xref:core/querying/related-data)). Allo stesso tempo, è preferibile rendere tali proprietà Nullable, in quanto ciò impone l'accesso a tutti gli accessi per verificare la presenza di valori null, anche se sono necessari.

Un modo per gestire questi scenari consiste nel disporre di una proprietà che non ammette i valori null con un [campo](xref:core/modeling/backing-field)sottostante Nullable:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

Poiché la proprietà di navigazione è non nullable, viene configurata una navigazione obbligatoria. fino a quando lo spostamento viene caricato correttamente, il dipendente sarà accessibile tramite la proprietà. Se, tuttavia, viene eseguito l'accesso alla proprietà senza prima caricare correttamente l'entità correlata, viene generata un'eccezione InvalidOperationException, perché il contratto API è stato usato in modo errato. Si noti che EF deve essere configurato per accedere sempre al campo sottostante e non alla proprietà, perché si basa sulla possibilità di leggere il valore anche quando viene annullato. per informazioni su come eseguire questa operazione, vedere la documentazione relativa ai [campi di supporto](xref:core/modeling/backing-field) e prendere in considerazione la possibilità di specificare `PropertyAccessMode.Field` per assicurarsi che la configurazione sia corretta.

Come alternativa Terser, è possibile semplicemente inizializzare la proprietà su null con l'ausilio dell'operatore con indulgenza null (!):

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

Un valore null effettivo non verrà mai osservato ad eccezione di come risultato di un bug di programmazione, ad esempio l'accesso alla proprietà di navigazione senza caricare correttamente l'entità correlata in precedenza.

> [!NOTE]
> Le esplorazioni della raccolta, che contengono riferimenti a più entità correlate, devono essere sempre non nullable. Una raccolta vuota significa che non esiste alcuna entità correlata, ma l'elenco stesso non deve mai essere null.

## <a name="dbcontext-and-dbset"></a>DbContext e DbSet

Anche la pratica comune di disporre di proprietà DbSet non inizializzate sui tipi di contesto è problematica, in quanto il compilatore emetterà avvisi per tali proprietà. Questo problema può essere risolto come segue:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

Un'altra strategia consiste nell'usare le proprietà automatiche che non ammettono i valori null, ma per inizializzarle su null, usando l'operatore di indulgenza null (!) per disattivare l'avviso del compilatore. Il costruttore di base DbContext garantisce che tutte le proprietà DbSet vengano inizializzate e null non verrà mai osservato su di essi.

## <a name="navigating-and-including-nullable-relationships"></a>Esplorazione e inclusione di relazioni Nullable

Quando si gestiscono relazioni facoltative, è possibile che si verifichino avvisi del compilatore in cui un'eccezione di riferimento null effettiva potrebbe non essere possibile. Quando si traducono ed eseguono le query LINQ, EF Core garantisce che se non esiste un'entità correlata facoltativa, eventuali spostamenti verranno semplicemente ignorati, anziché generare. Tuttavia, il compilatore non è a conoscenza di questo EF Core garanzia e genera avvisi come se la query LINQ venisse eseguita in memoria, con LINQ to Objects. Di conseguenza, è necessario usare l'operatore di perdonazione null (!) per informare il compilatore che non è possibile un valore null effettivo:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

Si verifica un problema simile quando si includono più livelli di relazioni tra gli spostamenti facoltativi:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

Se questa operazione viene eseguita molto e i tipi di entità in questione sono prevalentemente (o esclusivamente) usati nelle query di EF Core, è consigliabile rendere le proprietà di navigazione non nullable e configurarle come facoltative tramite l'API Fluent o le annotazioni dei dati. Tutti gli avvisi del compilatore vengono rimossi mantenendo la relazione facoltativa. Tuttavia, se le entità vengono attraversate all'esterno di EF Core, è possibile osservare valori null anche se le proprietà sono annotate come non nullable.

## <a name="limitations"></a>Limitazioni

* Il reverse engineering attualmente non supporta i [tipi di riferimento Nullable di C# 8 (NRTs)](/dotnet/csharp/tutorials/nullable-reference-types): EF core genera sempre codice c# che presuppone che la funzionalità sia disattivata. Ad esempio, le colonne di testo Nullable verranno sottoposto a impalcatura come proprietà con tipo `string` , non `string?` con l'API Fluent o le annotazioni dei dati utilizzate per configurare se una proprietà è obbligatoria o meno. È possibile modificare il codice con impalcature e sostituirle con annotazioni di supporto per i valori null C#. Il supporto dell'impalcatura per i tipi di riferimento nullable viene rilevato da Issue [#15520](https://github.com/dotnet/efcore/issues/15520).
* La superficie dell'API pubblica di EF Core non è ancora stata annotata per il supporto di valori null (l'API pubblica è "ignaro null"), rendendo talvolta scomodo da usare quando la funzionalità NRT è attivata. In particolare, include gli operatori LINQ asincroni esposti da EF Core, ad esempio [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_). Si prevede di risolvere questo problema per la versione 5,0.
