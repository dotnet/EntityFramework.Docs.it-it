---
title: Entità disconnesse - EF Core
description: Utilizzo di entità disconnesse e non registrate tra più istanze di contesto in Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063569"
---
# <a name="disconnected-entities"></a>Entità disconnesse

Un'istanza di DbContext sottoporrà automaticamente a rilevamento delle modifiche le entità restituite dal database. Le modifiche apportate a queste entità verranno quindi rilevate quando viene chiamato SaveChanges e il database verrà aggiornato in base alle esigenze. Vedere [Salvataggio di base](xref:core/saving/basic) e [Dati correlati](xref:core/saving/related-data) per informazioni dettagliate.

Tuttavia, a volte le entità vengono sottoposte a query usando un'istanza di contesto e poi salvate con un'istanza diversa. Questo accade spesso in scenari "disconnessi", ad esempio un'applicazione Web in cui le entità vengono recuperate tramite query, inviate al client, modificate, inviate al server in una richiesta e quindi salvate. In questo caso, la seconda istanza del contesto deve sapere se le entità sono nuove (devono essere inserite) o esistenti (devono essere aggiornate).

<!-- markdownlint-disable MD028 -->
> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) di questo articolo in GitHub.

> [!TIP]
> EF Core può eseguire il rilevamento delle modifiche per una sola istanza di qualsiasi entità con un determinato valore di chiave primaria. Il modo migliore per evitare che ciò diventi un problema consiste nell'usare un contesto di breve durata per ogni unità di lavoro, in modo che il contesto sia inizialmente vuoto, abbia entità collegate, salvi queste entità, per poi essere eliminato e rimosso.
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>Identificazione di nuove entità

### <a name="client-identifies-new-entities"></a>Nuove identità identificate dal client

Il caso più semplice da affrontare è quando il client comunica al server se l'entità è nuova o esistente. Ad esempio, spesso la richiesta di inserire una nuova entità è diversa dalla richiesta di aggiornare un'entità esistente.

Nella parte restante di questa sezione vengono illustrati i casi in cui è necessario determinare in altro modo se eseguire un inserimento o un aggiornamento.

### <a name="with-auto-generated-keys"></a>Con chiavi generate automaticamente

Il valore di una chiave generata automaticamente può essere spesso usato per determinare se un'entità deve essere inserita o aggiornata. Se la chiave non è stata impostata (ad esempio, ha ancora il valore predefinito di CLR Null, zero e così via), l'entità deve essere nuova e quindi inserita. D'altra parte, se il valore della chiave è stato impostato, l'entità deve essere già stata salvata in precedenza e ora richiede un aggiornamento. In altre parole, se la chiave ha un valore, allora l'entità è stata già sottoposta a query e inviata al client e ora ritorna per l'aggiornamento.

È facile verificare la presenza di una chiave non impostata quando è noto il tipo di entità:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

Tuttavia, EF include anche un modo predefinito per eseguire questa operazione per qualsiasi tipo di entità e tipo di chiave:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> Le chiavi vengono impostate non appena le entità vengono incluse nel rilevamento delle modifiche dal contesto, anche se l'entità risulta con lo stato Added. Ciò è utile durante l'attraversamento di un grafo di entità e per decidere come procedere con ognuna, ad esempio quando di usa l'API TrackGraph. Il valore della chiave deve essere usato solo nel modo illustrato di seguito _prima_ che venga effettuata qualsiasi chiamata per il rilevamento delle modifiche dell'entità.

### <a name="with-other-keys"></a>Con altre chiavi

È necessario un altro meccanismo per identificare le nuove entità quando i valori di chiave non vengono generati automaticamente. Esistono due approcci generali a questo scopo:

* Recuperare l'entità tramite query
* Passare un flag dal client

Per eseguire una query per l'entità, è sufficiente usare il metodo Find:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

Esula dagli scopi di questo documento mostrare il codice completo per il passaggio di un flag da un client. In un'app Web, in genere significa effettuare richieste diverse per azioni diverse oppure passare uno stato nella richiesta e quindi estrarlo nel controller.

## <a name="saving-single-entities"></a>Salvataggio di singole entità

Quando è noto se è necessario eseguire un inserimento o un aggiornamento, è possibile usare in modo appropriato Add o Update:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

Tuttavia, se l'entità usa valori di chiave generati automaticamente, è possibile usare il metodo Update per entrambi i casi:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Il metodo Update contrassegna in genere l'entità per l'aggiornamento e non per l'inserimento. Tuttavia, se l'entità ha una chiave generata automaticamente e non è stato impostato alcun valore per la chiave, l'entità viene invece contrassegnata automaticamente per l'inserimento.

Se l'entità non usa chiavi generate automaticamente, l'applicazione deve quindi decidere se l'entità deve essere inserita o aggiornata. Ad esempio:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

La procedura è la seguente:

* Se Find restituisce Null, il database non contiene già il blog con tale ID, pertanto si chiama Add per contrassegnarlo per l'inserimento.
* Se Find restituisce un'entità, significa che il blog esiste nel database e il contesto esegue il rilevamento delle modifiche per l'entità esistente
  * È quindi possibile usare SetValues per impostare i valori per tutte le proprietà dell'entità sui valori provenienti dal client.
  * La chiamata di SetValues contrassegnerà l'entità per l'aggiornamento in base alle esigenze.

> [!TIP]  
> SetValues contrassegnerà come modificate solo le proprietà con valori diversi da quelli nell'entità con rilevamento delle modifiche. Questo significa che quando viene inviato l'aggiornamento, verranno aggiornate solo le colonne effettivamente modificate. (In assenza di modifiche non verrà inviato alcun aggiornamento.)

## <a name="working-with-graphs"></a>Utilizzo dei grafi

### <a name="identity-resolution"></a>Risoluzione di identità

Come indicato in precedenza, EF Core può eseguire il rilevamento delle modifiche per una sola istanza di qualsiasi entità con un determinato valore di chiave primaria. Quando si utilizzano i grafi, il grafo deve essere creato idealmente in modo da mantenere questa invariante e da usare il contesto per una sola unità di lavoro. Se il grafo contiene duplicati, sarà necessario elaborare il grafo prima di inviarlo a EF per consolidare più istanze in una unica. Questa operazione potrebbe non essere semplice se le istanze hanno valori e relazioni in conflitto, quindi è consigliabile eseguire il consolidamento dei duplicati non appena possibile nella pipeline dell'applicazione per evitare la risoluzione dei conflitti.

### <a name="all-newall-existing-entities"></a>Tutte le entità nuove/esistenti

Un esempio di utilizzo dei grafi è l'inserimento o l'aggiornamento di un blog con la raccolta di post associati. Se tutte le entità nel grafo devono essere inserite o devono essere tutte aggiornate, il processo è identico a quello sopra descritto per singole entità. Ad esempio, un grafo di blog e post creato come il seguente:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

può essere inserito nel modo seguente:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

La chiamata di Add contrassegnerà il blog e tutti i post per l'inserimento.

Analogamente, se tutte le entità in un grafo devono essere aggiornate, si può usare Update:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

Il blog e tutti i relativi post verranno contrassegnati per l'aggiornamento.

### <a name="mix-of-new-and-existing-entities"></a>Combinazione di entità nuove ed esistenti

Con le chiavi generate automaticamente, è possibile usare Update sia per gli inserimenti che per gli aggiornamenti, anche se il grafo contiene una combinazione di entità che richiedono l'inserimento e che richiedono l'aggiornamento:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Update contrassegnerà qualsiasi entità nel grafo, blog o post, per l'inserimento, se non dispone di valore di chiave impostato, mentre tutte le altre entità verranno contrassegnate per l'aggiornamento.

Come prima, se non si usano chiavi generate automaticamente, è possibile usare una query e alcune operazioni di elaborazione:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>Gestione delle eliminazioni

L'eliminazione può essere difficile da gestire, dato che l'assenza di un'entità indica spesso che deve essere eliminata. Un modo per risolvere questo problema consiste nell'usare "eliminazioni temporanee" in modo che l'entità venga contrassegnata come eliminata anziché essere effettivamente eliminata. Le eliminazioni diventano quindi uguali agli aggiornamenti. Le eliminazioni temporanee possono essere implementate usando [filtri di query](xref:core/querying/filters).

Per le vere eliminazioni, un modello comune consiste nell'usare un'estensione del modello di query per eseguire essenzialmente un confronto delle differenze del grafo. Ad esempio:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

Internamente, Add, Attach e Update usano l'attraversamento del grafo determinando per ogni entità se deve essere contrassegnata come Added (per l'inserimento), Modified (per l'aggiornamento), Unchanged (per non eseguire alcuna operazione) o Delete (per l'eliminazione). Questo meccanismo viene esposto tramite l'API TrackGraph. Ad esempio, si supponga che quando il client invia un grafo delle entità imposti alcuni flag per ogni entità per indicare come deve essere gestita. TrackGraph può quindi essere usato per elaborare questo flag:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

I flag vengono visualizzati solo come parte dell'entità per semplicità dell'esempio. In genere, i flag farebbero parte di un DTO o qualche altro stato incluso nella richiesta.
