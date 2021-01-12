---
title: Entità disconnesse - EF Core
description: Utilizzo di entità disconnesse e non registrate tra più istanze di contesto in Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: 4ddae08b68aa99fb81de77fed8a195c60927e0bb
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128849"
---
# <a name="disconnected-entities"></a><span data-ttu-id="e8ce7-103">Entità disconnesse</span><span class="sxs-lookup"><span data-stu-id="e8ce7-103">Disconnected entities</span></span>

<span data-ttu-id="e8ce7-104">Un'istanza di DbContext sottoporrà automaticamente a rilevamento delle modifiche le entità restituite dal database.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="e8ce7-105">Le modifiche apportate a queste entità verranno quindi rilevate quando viene chiamato SaveChanges e il database verrà aggiornato in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="e8ce7-106">Vedere [Salvataggio di base](xref:core/saving/basic) e [Dati correlati](xref:core/saving/related-data) per informazioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="e8ce7-107">Tuttavia, a volte le entità vengono sottoposte a query usando un'istanza di contesto e poi salvate con un'istanza diversa.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="e8ce7-108">Questo accade spesso in scenari "disconnessi", ad esempio un'applicazione Web in cui le entità vengono recuperate tramite query, inviate al client, modificate, inviate al server in una richiesta e quindi salvate.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="e8ce7-109">In questo caso, la seconda istanza del contesto deve sapere se le entità sono nuove (devono essere inserite) o esistenti (devono essere aggiornate).</span><span class="sxs-lookup"><span data-stu-id="e8ce7-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="e8ce7-110">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="e8ce7-111">EF Core può eseguire il rilevamento delle modifiche per una sola istanza di qualsiasi entità con un determinato valore di chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="e8ce7-112">Il modo migliore per evitare che ciò diventi un problema consiste nell'usare un contesto di breve durata per ogni unità di lavoro, in modo che il contesto sia inizialmente vuoto, abbia entità collegate, salvi queste entità, per poi essere eliminato e rimosso.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="e8ce7-113">Identificazione di nuove entità</span><span class="sxs-lookup"><span data-stu-id="e8ce7-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="e8ce7-114">Nuove identità identificate dal client</span><span class="sxs-lookup"><span data-stu-id="e8ce7-114">Client identifies new entities</span></span>

<span data-ttu-id="e8ce7-115">Il caso più semplice da affrontare è quando il client comunica al server se l'entità è nuova o esistente.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="e8ce7-116">Ad esempio, spesso la richiesta di inserire una nuova entità è diversa dalla richiesta di aggiornare un'entità esistente.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="e8ce7-117">Nella parte restante di questa sezione vengono illustrati i casi in cui è necessario determinare in altro modo se eseguire un inserimento o un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="e8ce7-118">Con chiavi generate automaticamente</span><span class="sxs-lookup"><span data-stu-id="e8ce7-118">With auto-generated keys</span></span>

<span data-ttu-id="e8ce7-119">Il valore di una chiave generata automaticamente può essere spesso usato per determinare se un'entità deve essere inserita o aggiornata.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="e8ce7-120">Se la chiave non è stata impostata (ad esempio, ha ancora il valore predefinito di CLR Null, zero e così via), l'entità deve essere nuova e quindi inserita.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="e8ce7-121">D'altra parte, se il valore della chiave è stato impostato, l'entità deve essere già stata salvata in precedenza e ora richiede un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="e8ce7-122">In altre parole, se la chiave ha un valore, allora l'entità è stata già sottoposta a query e inviata al client e ora ritorna per l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="e8ce7-123">È facile verificare la presenza di una chiave non impostata quando è noto il tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="e8ce7-124">Tuttavia, EF include anche un modo predefinito per eseguire questa operazione per qualsiasi tipo di entità e tipo di chiave:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]
> <span data-ttu-id="e8ce7-125">Le chiavi vengono impostate non appena le entità vengono incluse nel rilevamento delle modifiche dal contesto, anche se l'entità risulta con lo stato Added.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="e8ce7-126">Ciò è utile durante l'attraversamento di un grafo di entità e per decidere come procedere con ognuna, ad esempio quando di usa l'API TrackGraph.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="e8ce7-127">Il valore della chiave deve essere usato solo nel modo illustrato di seguito _prima_ che venga effettuata qualsiasi chiamata per il rilevamento delle modifiche dell'entità.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="e8ce7-128">Con altre chiavi</span><span class="sxs-lookup"><span data-stu-id="e8ce7-128">With other keys</span></span>

<span data-ttu-id="e8ce7-129">È necessario un altro meccanismo per identificare le nuove entità quando i valori di chiave non vengono generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="e8ce7-130">Esistono due approcci generali a questo scopo:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="e8ce7-131">Recuperare l'entità tramite query</span><span class="sxs-lookup"><span data-stu-id="e8ce7-131">Query for the entity</span></span>
* <span data-ttu-id="e8ce7-132">Passare un flag dal client</span><span class="sxs-lookup"><span data-stu-id="e8ce7-132">Pass a flag from the client</span></span>

<span data-ttu-id="e8ce7-133">Per eseguire una query per l'entità, è sufficiente usare il metodo Find:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="e8ce7-134">Esula dagli scopi di questo documento mostrare il codice completo per il passaggio di un flag da un client.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="e8ce7-135">In un'app Web, in genere significa effettuare richieste diverse per azioni diverse oppure passare uno stato nella richiesta e quindi estrarlo nel controller.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="e8ce7-136">Salvataggio di singole entità</span><span class="sxs-lookup"><span data-stu-id="e8ce7-136">Saving single entities</span></span>

<span data-ttu-id="e8ce7-137">Quando è noto se è necessario eseguire un inserimento o un aggiornamento, è possibile usare in modo appropriato Add o Update:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="e8ce7-138">Tuttavia, se l'entità usa valori di chiave generati automaticamente, è possibile usare il metodo Update per entrambi i casi:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="e8ce7-139">Il metodo Update contrassegna in genere l'entità per l'aggiornamento e non per l'inserimento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="e8ce7-140">Tuttavia, se l'entità ha una chiave generata automaticamente e non è stato impostato alcun valore per la chiave, l'entità viene invece contrassegnata automaticamente per l'inserimento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

<span data-ttu-id="e8ce7-141">Se l'entità non usa chiavi generate automaticamente, l'applicazione deve quindi decidere se l'entità deve essere inserita o aggiornata. Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-141">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="e8ce7-142">La procedura è la seguente:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-142">The steps here are:</span></span>

* <span data-ttu-id="e8ce7-143">Se Find restituisce Null, il database non contiene già il blog con tale ID, pertanto si chiama Add per contrassegnarlo per l'inserimento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-143">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="e8ce7-144">Se Find restituisce un'entità, significa che il blog esiste nel database e il contesto esegue il rilevamento delle modifiche per l'entità esistente</span><span class="sxs-lookup"><span data-stu-id="e8ce7-144">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="e8ce7-145">È quindi possibile usare SetValues per impostare i valori per tutte le proprietà dell'entità sui valori provenienti dal client.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-145">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="e8ce7-146">La chiamata di SetValues contrassegnerà l'entità per l'aggiornamento in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-146">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]
> <span data-ttu-id="e8ce7-147">SetValues contrassegnerà come modificate solo le proprietà con valori diversi da quelli nell'entità con rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-147">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="e8ce7-148">Questo significa che quando viene inviato l'aggiornamento, verranno aggiornate solo le colonne effettivamente modificate.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-148">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="e8ce7-149">(In assenza di modifiche non verrà inviato alcun aggiornamento.)</span><span class="sxs-lookup"><span data-stu-id="e8ce7-149">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="e8ce7-150">Utilizzo dei grafi</span><span class="sxs-lookup"><span data-stu-id="e8ce7-150">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="e8ce7-151">Risoluzione di identità</span><span class="sxs-lookup"><span data-stu-id="e8ce7-151">Identity resolution</span></span>

<span data-ttu-id="e8ce7-152">Come indicato in precedenza, EF Core può eseguire il rilevamento delle modifiche per una sola istanza di qualsiasi entità con un determinato valore di chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-152">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="e8ce7-153">Quando si utilizzano i grafi, il grafo deve essere creato idealmente in modo da mantenere questa invariante e da usare il contesto per una sola unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-153">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="e8ce7-154">Se il grafo contiene duplicati, sarà necessario elaborare il grafo prima di inviarlo a EF per consolidare più istanze in una unica.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-154">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="e8ce7-155">Questa operazione potrebbe non essere semplice se le istanze hanno valori e relazioni in conflitto, quindi è consigliabile eseguire il consolidamento dei duplicati non appena possibile nella pipeline dell'applicazione per evitare la risoluzione dei conflitti.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-155">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="e8ce7-156">Tutte le entità nuove/esistenti</span><span class="sxs-lookup"><span data-stu-id="e8ce7-156">All new/all existing entities</span></span>

<span data-ttu-id="e8ce7-157">Un esempio di utilizzo dei grafi è l'inserimento o l'aggiornamento di un blog con la raccolta di post associati.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-157">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="e8ce7-158">Se tutte le entità nel grafo devono essere inserite o devono essere tutte aggiornate, il processo è identico a quello sopra descritto per singole entità.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-158">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="e8ce7-159">Ad esempio, un grafo di blog e post creato come il seguente:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-159">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="e8ce7-160">può essere inserito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-160">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="e8ce7-161">La chiamata di Add contrassegnerà il blog e tutti i post per l'inserimento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-161">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="e8ce7-162">Analogamente, se tutte le entità in un grafo devono essere aggiornate, si può usare Update:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-162">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="e8ce7-163">Il blog e tutti i relativi post verranno contrassegnati per l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-163">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="e8ce7-164">Combinazione di entità nuove ed esistenti</span><span class="sxs-lookup"><span data-stu-id="e8ce7-164">Mix of new and existing entities</span></span>

<span data-ttu-id="e8ce7-165">Con le chiavi generate automaticamente, è possibile usare Update sia per gli inserimenti che per gli aggiornamenti, anche se il grafo contiene una combinazione di entità che richiedono l'inserimento e che richiedono l'aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-165">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="e8ce7-166">Update contrassegnerà qualsiasi entità nel grafo, blog o post, per l'inserimento, se non dispone di valore di chiave impostato, mentre tutte le altre entità verranno contrassegnate per l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-166">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="e8ce7-167">Come prima, se non si usano chiavi generate automaticamente, è possibile usare una query e alcune operazioni di elaborazione:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-167">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="e8ce7-168">Gestione delle eliminazioni</span><span class="sxs-lookup"><span data-stu-id="e8ce7-168">Handling deletes</span></span>

<span data-ttu-id="e8ce7-169">L'eliminazione può essere difficile da gestire, dato che l'assenza di un'entità indica spesso che deve essere eliminata.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-169">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="e8ce7-170">Un modo per risolvere questo problema consiste nell'usare "eliminazioni temporanee" in modo che l'entità venga contrassegnata come eliminata anziché essere effettivamente eliminata.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-170">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="e8ce7-171">Le eliminazioni diventano quindi uguali agli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-171">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="e8ce7-172">Le eliminazioni temporanee possono essere implementate usando [filtri di query](xref:core/querying/filters).</span><span class="sxs-lookup"><span data-stu-id="e8ce7-172">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="e8ce7-173">Per le vere eliminazioni, un modello comune consiste nell'usare un'estensione del modello di query per eseguire essenzialmente un confronto delle differenze del grafo.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-173">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="e8ce7-174">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-174">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="e8ce7-175">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="e8ce7-175">TrackGraph</span></span>

<span data-ttu-id="e8ce7-176">Internamente, Add, Attach e Update usano l'attraversamento del grafo determinando per ogni entità se deve essere contrassegnata come Added (per l'inserimento), Modified (per l'aggiornamento), Unchanged (per non eseguire alcuna operazione) o Delete (per l'eliminazione).</span><span class="sxs-lookup"><span data-stu-id="e8ce7-176">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="e8ce7-177">Questo meccanismo viene esposto tramite l'API TrackGraph.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-177">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="e8ce7-178">Ad esempio, si supponga che quando il client invia un grafo delle entità imposti alcuni flag per ogni entità per indicare come deve essere gestita.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-178">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="e8ce7-179">TrackGraph può quindi essere usato per elaborare questo flag:</span><span class="sxs-lookup"><span data-stu-id="e8ce7-179">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="e8ce7-180">I flag vengono visualizzati solo come parte dell'entità per semplicità dell'esempio.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-180">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="e8ce7-181">In genere, i flag farebbero parte di un DTO o qualche altro stato incluso nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="e8ce7-181">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
