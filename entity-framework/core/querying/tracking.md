---
title: Confronto tra query di rilevamento e No-Tracking-EF Core
description: Informazioni sulle query di rilevamento e senza rilevamento in Entity Framework Core
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: b4c059f9a9b726697009589271e007bd1d2afd56
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430443"
---
# <a name="tracking-vs-no-tracking-queries"></a>Rilevamento e query No-Tracking

Il rilevamento del comportamento Controlla se Entity Framework Core manterrà le informazioni su un'istanza di entità nel relativo strumento di rilevamento delle modifiche. Se un'entità viene inclusa nel rilevamento delle modifiche, qualsiasi modifica individuata per l'entità verrà salvata in modo permanente nel database durante `SaveChanges()`. EF Core correggerà anche le proprietà di navigazione tra le entità in un risultato della query di rilevamento e le entità presenti nello strumento di rilevamento delle modifiche.

> [!NOTE]
> I [tipi di entità senza chiave](xref:core/modeling/keyless-entity-types) non vengono mai rilevati. Quando in questo articolo vengono citati i tipi di entità, si riferisce ai tipi di entità con una chiave definita.

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking) di questo articolo in GitHub.

## <a name="tracking-queries"></a>Query con rilevamento delle modifiche

Per impostazione predefinita, le query che restituiscono tipi di entità sono con rilevamento delle modifiche. Ciò significa che è possibile apportare modifiche a tali istanze di entità e rendere le modifiche rese permanente da `SaveChanges()` . Nell'esempio seguente la modifica della classificazione del blog verrà rilevata e salvata in modo permanente nel database durante `SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

Quando i risultati vengono restituiti in una query di rilevamento, EF Core verificherà se l'entità è già presente nel contesto. Se EF Core trova un'entità esistente, viene restituita la stessa istanza. EF Core non sovrascrive i valori correnti e originali delle proprietà dell'entità nella voce con i valori del database. Se l'entità non viene trovata nel contesto, EF Core creerà una nuova istanza dell'entità e la collegherà al contesto. I risultati della query non contengono alcuna entità, che viene aggiunta al contesto ma non ancora salvata nel database.

## <a name="no-tracking-queries"></a>Query senza registrazione

Le query senza rilevamento delle modifiche sono utili quando i risultati vengono usati in uno scenario di sola lettura. Sono più veloci da eseguire perché non è necessario impostare le informazioni sul rilevamento delle modifiche. Se non è necessario aggiornare le entità recuperate dal database, è necessario utilizzare una query senza rilevamento. È possibile scambiare una singola query senza tracciare. Nessuna query di rilevamento fornirà inoltre i risultati in base a ciò che si trova nel database, a causa delle modifiche locali o delle entità aggiunte.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

È anche possibile modificare il comportamento predefinito di rilevamento delle modifiche a livello di istanza di contesto:

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>Risoluzione di identità

Poiché una query di rilevamento USA change tracker, EF Core eseguirà la risoluzione delle identità in una query di rilevamento. Quando si materializzazione un'entità, EF Core restituirà la stessa istanza dell'entità dallo strumento di rilevamento delle modifiche, se è già stata rilevata. Se il risultato contiene la stessa entità più volte, viene restituita la stessa istanza per ogni occorrenza. Le query senza rilevamento non usano change tracker e non eseguono la risoluzione delle identità. Quindi, si ottiene una nuova istanza dell'entità anche quando la stessa entità è contenuta nel risultato più volte. Questo comportamento è diverso nelle versioni precedenti EF Core 3,0, vedere [versioni precedenti](#previous-versions).

A partire da EF Core 5,0, è possibile combinare entrambi i comportamenti precedenti nella stessa query. Ovvero, è possibile avere una query senza rilevamento, che eseguirà la risoluzione delle identità nei risultati. Analogamente all' `AsNoTracking()` operatore Queryable, è stato aggiunto un altro operatore `AsNoTrackingWithIdentityResolution()` . In Enum è stata aggiunta anche la voce associata <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior> . Quando si configura la query in modo che utilizzi la risoluzione delle identità senza rilevamento, viene utilizzato uno strumento di rilevamento delle modifiche autonomo in background durante la generazione dei risultati della query in modo che ogni istanza venga materializzata una sola volta. Poiché questo strumento di rilevamento delle modifiche è diverso da quello nel contesto, i risultati non vengono rilevati dal contesto. Dopo che la query è stata enumerata completamente, lo strumento di rilevamento delle modifiche esce dall'ambito e sottoposto a Garbage Collection come richiesto.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

## <a name="tracking-and-custom-projections"></a>Rilevamento e proiezioni personalizzate

Anche se il tipo di risultato della query non è un tipo di entità, EF Core continuerà a tenere traccia dei tipi di entità contenuti nel risultato per impostazione predefinita. Nella query seguente, che restituisce un tipo anonimo, le istanze di `Blog` nel set di risultati verranno incluse nel rilevamento delle modifiche.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

Se il set di risultati contiene tipi di entità provenienti dalla composizione LINQ, EF Core li terrà traccia.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

Se il set di risultati non contiene tipi di entità, non viene eseguita alcuna verifica. Nella query seguente viene restituito un tipo anonimo con alcuni valori dell'entità, ma nessuna istanza del tipo di entità effettivo. Nessuna entità rilevata esce dalla query.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core supporta la valutazione dei client nella proiezione di primo livello. Se EF Core materializza un'istanza di entità per la valutazione client, verrà rilevata. Qui, poiché `blog` le entità vengono passate al metodo client `StandardizeURL` , EF Core tiene traccia anche delle istanze del Blog.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core non tiene traccia delle istanze di entità senza chiave contenute nel risultato. Tuttavia EF Core tiene traccia di tutte le altre istanze dei tipi di entità con chiave in base alle regole precedenti.

Alcune delle regole precedenti hanno funzionato in modo diverso prima di EF Core 3,0. Per ulteriori informazioni, vedere [versioni precedenti](#previous-versions).

## <a name="previous-versions"></a>Versioni precedenti

Prima della versione 3,0, EF Core aveva alcune differenze nel modo in cui il rilevamento è stato eseguito. Di seguito sono riportate le differenze rilevanti:

- Come illustrato nella pagina di [valutazione del client rispetto al server](xref:core/querying/client-eval) , EF core la valutazione client supportata in qualsiasi parte della query prima della versione 3,0. La valutazione client ha causato la materializzazione delle entità, che non fanno parte del risultato. Quindi EF Core analizzato il risultato per rilevare gli elementi di cui tenere traccia. Questa progettazione presenta alcune differenze, come indicato di seguito:
  - Valutazione client nella proiezione, che ha causato la materializzazione ma non ha restituito l'istanza di entità materializzata non è stata rilevata. Nell'esempio seguente non è stata tenuta traccia delle `blog` entità.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - In alcuni casi EF Core non tiene traccia degli oggetti che provengono dalla composizione LINQ. L'esempio seguente non è stato rilevato `Post` .
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- Ogni volta che i risultati della query contengono tipi di entità senza chiave, l'intera query è stata eseguita senza rilevamento. Ciò significa che i tipi di entità con chiavi, che non sono presenti nel risultato, non sono stati rilevati.
- EF Core ha fatto la risoluzione delle identità in una query senza rilevamento. Sono stati usati riferimenti deboli per tenere traccia delle entità che erano già state restituite. Quindi, se un set di risultati contiene la stessa entità più volte, si otterrebbe la stessa istanza per ogni occorrenza. Tuttavia, se un risultato precedente con la stessa identità è uscito dall'ambito e viene sottoposta a Garbage Collection, EF Core ha restituito una nuova istanza.
