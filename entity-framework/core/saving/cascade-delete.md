---
title: Eliminazione a catena - EF Core
description: Configurazione dei comportamenti di eliminazione per entità correlate quando viene eliminata un'entità principale
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/cascade-delete
ms.openlocfilehash: 037b018c669da76a70f134e3991ad22b36917920
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063608"
---
# <a name="cascade-delete"></a><span data-ttu-id="f59af-103">Eliminazione a catena</span><span class="sxs-lookup"><span data-stu-id="f59af-103">Cascade Delete</span></span>

<span data-ttu-id="f59af-104">Nella terminologia dei database, il termine eliminazione a catena viene usato comunemente per descrivere una caratteristica che consente di attivare automaticamente l'eliminazione di righe correlate in seguito all'eliminazione di una riga.</span><span class="sxs-lookup"><span data-stu-id="f59af-104">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="f59af-105">Un concetto strettamente correlato, anch'esso coperto dai comportamenti di eliminazione di EF Core, è l'eliminazione automatica di un'entità figlio quando la relazione con un'entità padre è stata interrotta, operazione comunemente nota come "eliminazione degli orfani".</span><span class="sxs-lookup"><span data-stu-id="f59af-105">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this is commonly known as "deleting orphans".</span></span>

<span data-ttu-id="f59af-106">EF Core implementa vari comportamenti di eliminazione diversi e consente di configurare i comportamenti di eliminazione di singole relazioni.</span><span class="sxs-lookup"><span data-stu-id="f59af-106">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="f59af-107">EF Core implementa anche convenzioni per la configurazione automatica di comportamenti di eliminazione predefiniti utili per ogni relazione in base alla [obbligatorietà della relazione](xref:core/modeling/relationships#required-and-optional-relationships).</span><span class="sxs-lookup"><span data-stu-id="f59af-107">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship](xref:core/modeling/relationships#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="f59af-108">Comportamenti di eliminazione</span><span class="sxs-lookup"><span data-stu-id="f59af-108">Delete behaviors</span></span>

<span data-ttu-id="f59af-109">I comportamenti di eliminazione vengono definiti nel tipo di enumeratore *DeleteBehavior* e possono essere passati all'API fluent *OnDelete* per controllare se l'eliminazione di un'entità principale/padre o l'interruzione della relazione con le entità dipendenti/figlio deve avere un effetto collaterale sulle entità dipendenti/figlio.</span><span class="sxs-lookup"><span data-stu-id="f59af-109">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="f59af-110">Esistono tre azioni che EF può eseguire quando viene eliminata un'entità principale/padre o viene interrotta la relazione con l'entità figlio:</span><span class="sxs-lookup"><span data-stu-id="f59af-110">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>

* <span data-ttu-id="f59af-111">L'entità figlio/dipendente può essere eliminata</span><span class="sxs-lookup"><span data-stu-id="f59af-111">The child/dependent can be deleted</span></span>
* <span data-ttu-id="f59af-112">I valori di chiave esterna dell'entità figlio possono essere impostati su Null</span><span class="sxs-lookup"><span data-stu-id="f59af-112">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="f59af-113">L'entità figlio rimane invariata</span><span class="sxs-lookup"><span data-stu-id="f59af-113">The child remains unchanged</span></span>

> [!NOTE]
> <span data-ttu-id="f59af-114">Il comportamento di eliminazione configurato nel modello di EF Core viene applicato solo quando l'entità principale viene eliminata usando EF Core e le entità dipendenti vengono caricate in memoria (come nel caso delle entità dipendenti con rilevamento delle modifiche).</span><span class="sxs-lookup"><span data-stu-id="f59af-114">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (that is, for tracked dependents).</span></span> <span data-ttu-id="f59af-115">È necessario configurare un comportamento a catena corrispondente nel database per garantire l'applicazione dell'azione necessaria ai dati non sottoposti a rilevamento delle modifiche dal contesto.</span><span class="sxs-lookup"><span data-stu-id="f59af-115">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="f59af-116">Se si usa EF Core per creare il database, questo comportamento a catena verrà configurato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f59af-116">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="f59af-117">Per la seconda azione indicata in precedenza, l'impostazione di un valore di chiave esterna su Null non è valida se la chiave esterna non ammette valori Null.</span><span class="sxs-lookup"><span data-stu-id="f59af-117">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="f59af-118">Una chiave esterna che non ammette i valori null equivale a una relazione obbligatoria. In questi casi, EF Core rileva che la proprietà della chiave esterna è stata contrassegnata come null finché non viene chiamato SaveChanges, a quel punto viene generata un'eccezione in quanto la modifica non può essere resa permanente nel database.</span><span class="sxs-lookup"><span data-stu-id="f59af-118">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="f59af-119">La situazione è simile alla segnalazione di una violazione di vincolo dal database.</span><span class="sxs-lookup"><span data-stu-id="f59af-119">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="f59af-120">Sono disponibili quattro comportamenti di eliminazione, elencati nelle tabelle seguenti.</span><span class="sxs-lookup"><span data-stu-id="f59af-120">There are four delete behaviors, as listed in the tables below.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="f59af-121">Relazioni facoltative</span><span class="sxs-lookup"><span data-stu-id="f59af-121">Optional relationships</span></span>

<span data-ttu-id="f59af-122">Per le relazioni facoltative (chiave esterna che ammette valori Null), _è_ possibile salvare un valore di chiave esterna Null, con gli effetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f59af-122">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="f59af-123">Nome del comportamento</span><span class="sxs-lookup"><span data-stu-id="f59af-123">Behavior Name</span></span>               | <span data-ttu-id="f59af-124">Effetto sull'entità dipendente/figlio in memoria</span><span class="sxs-lookup"><span data-stu-id="f59af-124">Effect on dependent/child in memory</span></span>    | <span data-ttu-id="f59af-125">Effetto sull'entità dipendente/figlio nel database</span><span class="sxs-lookup"><span data-stu-id="f59af-125">Effect on dependent/child in database</span></span>  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| <span data-ttu-id="f59af-126">**Cascata**</span><span class="sxs-lookup"><span data-stu-id="f59af-126">**Cascade**</span></span>                 | <span data-ttu-id="f59af-127">Le entità vengono eliminate</span><span class="sxs-lookup"><span data-stu-id="f59af-127">Entities are deleted</span></span>                   | <span data-ttu-id="f59af-128">Le entità vengono eliminate</span><span class="sxs-lookup"><span data-stu-id="f59af-128">Entities are deleted</span></span>                   |
| <span data-ttu-id="f59af-129">**ClientSetNull** (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="f59af-129">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="f59af-130">Le proprietà di chiave esterna vengono impostate su Null</span><span class="sxs-lookup"><span data-stu-id="f59af-130">Foreign key properties are set to null</span></span> | <span data-ttu-id="f59af-131">Nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-131">None</span></span>                                   |
| <span data-ttu-id="f59af-132">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="f59af-132">**SetNull**</span></span>                 | <span data-ttu-id="f59af-133">Le proprietà di chiave esterna vengono impostate su Null</span><span class="sxs-lookup"><span data-stu-id="f59af-133">Foreign key properties are set to null</span></span> | <span data-ttu-id="f59af-134">Le proprietà di chiave esterna vengono impostate su Null</span><span class="sxs-lookup"><span data-stu-id="f59af-134">Foreign key properties are set to null</span></span> |
| <span data-ttu-id="f59af-135">**Limitare**</span><span class="sxs-lookup"><span data-stu-id="f59af-135">**Restrict**</span></span>                | <span data-ttu-id="f59af-136">nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-136">None</span></span>                                   | <span data-ttu-id="f59af-137">nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-137">None</span></span>                                   |

### <a name="required-relationships"></a><span data-ttu-id="f59af-138">Relazioni obbligatorie</span><span class="sxs-lookup"><span data-stu-id="f59af-138">Required relationships</span></span>

<span data-ttu-id="f59af-139">Per le relazioni obbligatorie (chiave esterna che non ammette valori Null), _non_ è possibile salvare un valore di chiave esterna Null, con gli effetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f59af-139">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="f59af-140">Nome del comportamento</span><span class="sxs-lookup"><span data-stu-id="f59af-140">Behavior Name</span></span>         | <span data-ttu-id="f59af-141">Effetto sull'entità dipendente/figlio in memoria</span><span class="sxs-lookup"><span data-stu-id="f59af-141">Effect on dependent/child in memory</span></span> | <span data-ttu-id="f59af-142">Effetto sull'entità dipendente/figlio nel database</span><span class="sxs-lookup"><span data-stu-id="f59af-142">Effect on dependent/child in database</span></span> |
|:----------------------|:------------------------------------|:--------------------------------------|
| <span data-ttu-id="f59af-143">**Cascade** (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="f59af-143">**Cascade** (Default)</span></span> | <span data-ttu-id="f59af-144">Le entità vengono eliminate</span><span class="sxs-lookup"><span data-stu-id="f59af-144">Entities are deleted</span></span>                | <span data-ttu-id="f59af-145">Le entità vengono eliminate</span><span class="sxs-lookup"><span data-stu-id="f59af-145">Entities are deleted</span></span>                  |
| <span data-ttu-id="f59af-146">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="f59af-146">**ClientSetNull**</span></span>     | <span data-ttu-id="f59af-147">SaveChanges genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="f59af-147">SaveChanges throws</span></span>                  | <span data-ttu-id="f59af-148">Nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-148">None</span></span>                                  |
| <span data-ttu-id="f59af-149">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="f59af-149">**SetNull**</span></span>           | <span data-ttu-id="f59af-150">SaveChanges genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="f59af-150">SaveChanges throws</span></span>                  | <span data-ttu-id="f59af-151">SaveChanges genera un'eccezione</span><span class="sxs-lookup"><span data-stu-id="f59af-151">SaveChanges throws</span></span>                    |
| <span data-ttu-id="f59af-152">**Limitare**</span><span class="sxs-lookup"><span data-stu-id="f59af-152">**Restrict**</span></span>          | <span data-ttu-id="f59af-153">nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-153">None</span></span>                                | <span data-ttu-id="f59af-154">nessuno</span><span class="sxs-lookup"><span data-stu-id="f59af-154">None</span></span>                                  |

<span data-ttu-id="f59af-155">Nelle tabelle precedenti, la condizione *Nessuno* può comportare una violazione di vincolo.</span><span class="sxs-lookup"><span data-stu-id="f59af-155">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="f59af-156">Ad esempio, se viene eliminata un'entità principale/figlio, ma non viene eseguita alcuna azione per modificare la chiave esterna di un'entità dipendente/figlio, il database genererà probabilmente eccezioni durante SaveChanges a causa di una violazione del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="f59af-156">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="f59af-157">In generale:</span><span class="sxs-lookup"><span data-stu-id="f59af-157">At a high level:</span></span>

* <span data-ttu-id="f59af-158">In presenza di entità che non possono esistere senza un padre e se si vuole che EF gestisca automaticamente l'eliminazione delle entità figlio, usare *Cascade*.</span><span class="sxs-lookup"><span data-stu-id="f59af-158">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="f59af-159">Le entità che non possono esistere senza un'entità padre in genere usano relazioni obbligatorie, per le quali *Cascade* è l'impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f59af-159">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="f59af-160">Con entità che possono o meno avere un'entità padre e se si vuole che EF gestisca automaticamente l'impostazione su Null della chiave esterna, usare *ClientSetNull*</span><span class="sxs-lookup"><span data-stu-id="f59af-160">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="f59af-161">Le entità che possono esistere senza un'entità padre in genere usano relazioni facoltative, per le quali *ClientSetNull* è l'impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f59af-161">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="f59af-162">Se si vuole che il database tenti inoltre la propagazione dei valori Null alle chiavi esterne figlio, anche quando l'entità figlio non viene caricata, usare *SetNull*.</span><span class="sxs-lookup"><span data-stu-id="f59af-162">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="f59af-163">Si noti, tuttavia, che il database deve supportare queste operazioni e la configurazione del database in questo modo può comportare altre restrizioni, rendendo spesso questa opzione impraticabile.</span><span class="sxs-lookup"><span data-stu-id="f59af-163">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="f59af-164">Questo è il motivo per cui *SetNull* non è l'impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f59af-164">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="f59af-165">Se non si vuole che Core EF elimini mai automaticamente un'entità o imposti automaticamente su Null la chiave esterna, usare *Restrict*.</span><span class="sxs-lookup"><span data-stu-id="f59af-165">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="f59af-166">Si noti che questa operazione richiede che il codice mantenga sincronizzati manualmente le entità figlio e i relativi valori di chiave esterna. In caso contrario verranno generate eccezioni per i vincoli.</span><span class="sxs-lookup"><span data-stu-id="f59af-166">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="f59af-167">In EF Core, a differenza di EF6, gli effetti a catena non vengono eseguiti immediatamente, ma solo quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f59af-167">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="f59af-168">Esempi di eliminazione di entità</span><span class="sxs-lookup"><span data-stu-id="f59af-168">Entity deletion examples</span></span>

<span data-ttu-id="f59af-169">Il codice seguente fa parte di un [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) che può essere scaricato ed eseguito.</span><span class="sxs-lookup"><span data-stu-id="f59af-169">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="f59af-170">L'esempio mostra cosa accade per ogni comportamento di eliminazione sia per le relazioni obbligatorie che facoltative, quando viene eliminata un'entità padre.</span><span class="sxs-lookup"><span data-stu-id="f59af-170">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="f59af-171">Ogni variazione verrà analizzata per capire il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="f59af-171">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="f59af-172">DeleteBehavior.Cascade con relazione obbligatoria o facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-172">DeleteBehavior.Cascade with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="f59af-173">Il blog viene contrassegnato come eliminato (Deleted)</span><span class="sxs-lookup"><span data-stu-id="f59af-173">Blog is marked as Deleted</span></span>
* <span data-ttu-id="f59af-174">I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f59af-174">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="f59af-175">SaveChanges invia eliminazioni per entrambe le entità dipendenti/figlio (post) e poi per l'entità principale/padre (blog)</span><span class="sxs-lookup"><span data-stu-id="f59af-175">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="f59af-176">Dopo il salvataggio, tutte le entità vengono scollegate perché a questo punto sono state eliminate dal database</span><span class="sxs-lookup"><span data-stu-id="f59af-176">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="f59af-177">DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione obbligatoria</span><span class="sxs-lookup"><span data-stu-id="f59af-177">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="f59af-178">Il blog viene contrassegnato come eliminato (Deleted)</span><span class="sxs-lookup"><span data-stu-id="f59af-178">Blog is marked as Deleted</span></span>
* <span data-ttu-id="f59af-179">I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f59af-179">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="f59af-180">SaveChanges tenta di impostare la chiave esterna del post su Null, ma l'operazione non riesce perché la chiave esterna non ammette valori Null</span><span class="sxs-lookup"><span data-stu-id="f59af-180">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="f59af-181">DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-181">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="f59af-182">Il blog viene contrassegnato come eliminato (Deleted)</span><span class="sxs-lookup"><span data-stu-id="f59af-182">Blog is marked as Deleted</span></span>
* <span data-ttu-id="f59af-183">I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f59af-183">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="f59af-184">SaveChanges tenta di impostare la chiave esterna di entrambe le entità dipendenti/figlio (post) su Null prima di eliminare l'entità principale/padre (blog)</span><span class="sxs-lookup"><span data-stu-id="f59af-184">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="f59af-185">Dopo il salvataggio, l'entità principale/padre (blog) viene eliminata, ma le entità dipendenti/figlio (post) sono ancora incluse nel rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="f59af-185">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="f59af-186">Le entità dipendenti/figlio (post) incluse nel rilevamento delle modifiche hanno ora valori di chiave esterna Null e il riferimento all'entità principale/padre (blog) è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="f59af-186">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="f59af-187">DeleteBehavior.Restrict con relazione obbligatoria o facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-187">DeleteBehavior.Restrict with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="f59af-188">Il blog viene contrassegnato come eliminato (Deleted)</span><span class="sxs-lookup"><span data-stu-id="f59af-188">Blog is marked as Deleted</span></span>
* <span data-ttu-id="f59af-189">I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f59af-189">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="f59af-190">Poiché *Restrict* indica a EF di non impostare automaticamente la chiave esterna su Null, la chiave rimane non Null e SaveChanges genera un'eccezione senza salvare le modifiche</span><span class="sxs-lookup"><span data-stu-id="f59af-190">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="f59af-191">Esempi di eliminazione degli orfani</span><span class="sxs-lookup"><span data-stu-id="f59af-191">Delete orphans examples</span></span>

<span data-ttu-id="f59af-192">Il codice seguente fa parte di un [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) che può essere scaricato ed eseguito.</span><span class="sxs-lookup"><span data-stu-id="f59af-192">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="f59af-193">L'esempio illustra le conseguenze di ogni comportamento di eliminazione sia per le relazioni facoltative che per quelle obbligatorie, quando viene interrotta la relazione tra un'entità padre/principale e le relative entità figlio/dipendenti.</span><span class="sxs-lookup"><span data-stu-id="f59af-193">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="f59af-194">In questo esempio, la relazione viene interrotta rimuovendo gli oggetti dipendenti o elementi figlio (POST) dalla proprietà di navigazione della raccolta nell'entità principale/padre (blog).</span><span class="sxs-lookup"><span data-stu-id="f59af-194">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="f59af-195">Tuttavia, il comportamento è lo stesso se il riferimento da entità dipendente/figlio a entità principale/padre viene invece impostato su Null.</span><span class="sxs-lookup"><span data-stu-id="f59af-195">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="f59af-196">Ogni variazione verrà analizzata per capire il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="f59af-196">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="f59af-197">DeleteBehavior.Cascade con relazione obbligatoria o facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-197">DeleteBehavior.Cascade with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="f59af-198">I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-198">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="f59af-199">Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-199">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="f59af-200">SaveChanges invia eliminazioni per le entità dipendenti/figlio (post)</span><span class="sxs-lookup"><span data-stu-id="f59af-200">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="f59af-201">Dopo il salvataggio, le entità dipendenti/figlio (post) vengono scollegate perché a questo punto sono state eliminate dal database</span><span class="sxs-lookup"><span data-stu-id="f59af-201">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="f59af-202">DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione obbligatoria</span><span class="sxs-lookup"><span data-stu-id="f59af-202">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="f59af-203">I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-203">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="f59af-204">Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-204">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="f59af-205">SaveChanges tenta di impostare la chiave esterna del post su Null, ma l'operazione non riesce perché la chiave esterna non ammette valori Null</span><span class="sxs-lookup"><span data-stu-id="f59af-205">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="f59af-206">DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-206">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="f59af-207">I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-207">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="f59af-208">Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-208">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="f59af-209">SaveChanges imposta la chiave esterna di entrambe le entità dipendenti/figlio (post) su Null</span><span class="sxs-lookup"><span data-stu-id="f59af-209">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="f59af-210">Dopo il salvataggio, le entità dipendenti/figlio (post) hanno ora valori di chiave esterna Null e il riferimento all'entità principale/padre (blog) è stato rimosso</span><span class="sxs-lookup"><span data-stu-id="f59af-210">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="f59af-211">DeleteBehavior.Restrict con relazione obbligatoria o facoltativa</span><span class="sxs-lookup"><span data-stu-id="f59af-211">DeleteBehavior.Restrict with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="f59af-212">I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-212">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="f59af-213">Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null</span><span class="sxs-lookup"><span data-stu-id="f59af-213">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="f59af-214">Poiché *Restrict* indica a EF di non impostare automaticamente la chiave esterna su Null, la chiave rimane non Null e SaveChanges genera un'eccezione senza salvare le modifiche</span><span class="sxs-lookup"><span data-stu-id="f59af-214">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="f59af-215">Applicazione a catena alle entità senza rilevamento delle modifiche</span><span class="sxs-lookup"><span data-stu-id="f59af-215">Cascading to untracked entities</span></span>

<span data-ttu-id="f59af-216">Quando si chiama *SaveChanges*, le regole di eliminazione a catena verranno applicate a tutte le entità incluse nel rilevamento delle modifiche dal contesto.</span><span class="sxs-lookup"><span data-stu-id="f59af-216">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="f59af-217">Si tratta della situazione esistente in tutti gli esempi illustrati in precedenza, che è il motivo per cui vengono generate istruzioni SQL per eliminare sia l'entità principale/padre (blog) che tutte le entità dipendenti/figlio (post):</span><span class="sxs-lookup"><span data-stu-id="f59af-217">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
DELETE FROM [Posts] WHERE [PostId] = 1
DELETE FROM [Posts] WHERE [PostId] = 2
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="f59af-218">Se viene caricata solo l'entità principale, ad esempio, quando viene eseguita una query per un blog senza `Include(b => b.Posts)` per includere anche i post, SaveChanges genererà solo le istruzioni SQL per eliminare l'entità principale/padre:</span><span class="sxs-lookup"><span data-stu-id="f59af-218">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="f59af-219">Le entità dipendenti/figlio (post) verranno eliminate solo se per il database è configurato un comportamento a catena corrispondente.</span><span class="sxs-lookup"><span data-stu-id="f59af-219">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="f59af-220">Se si usa EF per creare il database, questo comportamento a catena verrà configurato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f59af-220">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
