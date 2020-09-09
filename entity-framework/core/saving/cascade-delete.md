---
title: Eliminazione a catena - EF Core
description: Configurazione dei comportamenti di eliminazione per entità correlate quando viene eliminata un'entità principale
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
uid: core/saving/cascade-delete
ms.openlocfilehash: cf17e6586b89376b2d7fcc0f9cbfc8e1c4f6ba58
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617401"
---
# <a name="cascade-delete"></a>Eliminazione a catena

Nella terminologia dei database, il termine eliminazione a catena viene usato comunemente per descrivere una caratteristica che consente di attivare automaticamente l'eliminazione di righe correlate in seguito all'eliminazione di una riga. Un concetto strettamente correlato, anch'esso coperto dai comportamenti di eliminazione di EF Core, è l'eliminazione automatica di un'entità figlio quando la relazione con un'entità padre è stata interrotta, operazione comunemente nota come "eliminazione degli orfani".

EF Core implementa vari comportamenti di eliminazione diversi e consente di configurare i comportamenti di eliminazione di singole relazioni. EF Core implementa anche convenzioni per la configurazione automatica di comportamenti di eliminazione predefiniti utili per ogni relazione in base alla [obbligatorietà della relazione](xref:core/modeling/relationships#required-and-optional-relationships).

## <a name="delete-behaviors"></a>Comportamenti di eliminazione

I comportamenti di eliminazione vengono definiti nel tipo di enumeratore *DeleteBehavior* e possono essere passati all'API fluent *OnDelete* per controllare se l'eliminazione di un'entità principale/padre o l'interruzione della relazione con le entità dipendenti/figlio deve avere un effetto collaterale sulle entità dipendenti/figlio.

Esistono tre azioni che EF può eseguire quando viene eliminata un'entità principale/padre o viene interrotta la relazione con l'entità figlio:

* L'entità figlio/dipendente può essere eliminata
* I valori di chiave esterna dell'entità figlio possono essere impostati su Null
* L'entità figlio rimane invariata

> [!NOTE]  
> Il comportamento di eliminazione configurato nel modello di EF Core viene applicato solo quando l'entità principale viene eliminata usando EF Core e le entità dipendenti vengono caricate in memoria (come nel caso delle entità dipendenti con rilevamento delle modifiche). È necessario configurare un comportamento a catena corrispondente nel database per garantire l'applicazione dell'azione necessaria ai dati non sottoposti a rilevamento delle modifiche dal contesto. Se si usa EF Core per creare il database, questo comportamento a catena verrà configurato automaticamente.

Per la seconda azione indicata in precedenza, l'impostazione di un valore di chiave esterna su Null non è valida se la chiave esterna non ammette valori Null. Una chiave esterna che non ammette i valori null equivale a una relazione obbligatoria. In questi casi, EF Core rileva che la proprietà della chiave esterna è stata contrassegnata come null finché non viene chiamato SaveChanges, a quel punto viene generata un'eccezione in quanto la modifica non può essere resa permanente nel database. La situazione è simile alla segnalazione di una violazione di vincolo dal database.

Sono disponibili quattro comportamenti di eliminazione, elencati nelle tabelle seguenti.

### <a name="optional-relationships"></a>Relazioni facoltative

Per le relazioni facoltative (chiave esterna che ammette valori Null), _è_ possibile salvare un valore di chiave esterna Null, con gli effetti seguenti:

| Nome del comportamento               | Effetto sull'entità dipendente/figlio in memoria    | Effetto sull'entità dipendente/figlio nel database  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascata**                 | Le entità vengono eliminate                   | Le entità vengono eliminate                   |
| **ClientSetNull** (impostazione predefinita) | Le proprietà di chiave esterna vengono impostate su Null | Nessuno                                   |
| **SetNull**                 | Le proprietà di chiave esterna vengono impostate su Null | Le proprietà di chiave esterna vengono impostate su Null |
| **Limitare**                | nessuno                                   | nessuno                                   |

### <a name="required-relationships"></a>Relazioni obbligatorie

Per le relazioni obbligatorie (chiave esterna che non ammette valori Null), _non_ è possibile salvare un valore di chiave esterna Null, con gli effetti seguenti:

| Nome del comportamento         | Effetto sull'entità dipendente/figlio in memoria | Effetto sull'entità dipendente/figlio nel database |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (impostazione predefinita) | Le entità vengono eliminate                | Le entità vengono eliminate                  |
| **ClientSetNull**     | SaveChanges genera un'eccezione                  | Nessuno                                  |
| **SetNull**           | SaveChanges genera un'eccezione                  | SaveChanges genera un'eccezione                    |
| **Limitare**          | nessuno                                | nessuno                                  |

Nelle tabelle precedenti, la condizione *Nessuno* può comportare una violazione di vincolo. Ad esempio, se viene eliminata un'entità principale/figlio, ma non viene eseguita alcuna azione per modificare la chiave esterna di un'entità dipendente/figlio, il database genererà probabilmente eccezioni durante SaveChanges a causa di una violazione del vincolo di chiave esterna.

In generale:

* In presenza di entità che non possono esistere senza un padre e se si vuole che EF gestisca automaticamente l'eliminazione delle entità figlio, usare *Cascade*.
  * Le entità che non possono esistere senza un'entità padre in genere usano relazioni obbligatorie, per le quali *Cascade* è l'impostazione predefinita.
* Con entità che possono o meno avere un'entità padre e se si vuole che EF gestisca automaticamente l'impostazione su Null della chiave esterna, usare *ClientSetNull*
  * Le entità che possono esistere senza un'entità padre in genere usano relazioni facoltative, per le quali *ClientSetNull* è l'impostazione predefinita.
  * Se si vuole che il database tenti inoltre la propagazione dei valori Null alle chiavi esterne figlio, anche quando l'entità figlio non viene caricata, usare *SetNull*. Si noti, tuttavia, che il database deve supportare queste operazioni e la configurazione del database in questo modo può comportare altre restrizioni, rendendo spesso questa opzione impraticabile. Questo è il motivo per cui *SetNull* non è l'impostazione predefinita.
* Se non si vuole che Core EF elimini mai automaticamente un'entità o imposti automaticamente su Null la chiave esterna, usare *Restrict*. Si noti che questa operazione richiede che il codice mantenga sincronizzati manualmente le entità figlio e i relativi valori di chiave esterna. In caso contrario verranno generate eccezioni per i vincoli.

> [!NOTE]
> In EF Core, a differenza di EF6, gli effetti a catena non vengono eseguiti immediatamente, ma solo quando viene chiamato SaveChanges.

> [!NOTE]  
> **Modifiche in EF Core 2.0:** nelle versioni precedenti *Restrict* causerebbe l'impostazione su Null delle proprietà di chiave esterna facoltative nelle entità dipendenti sottoposte a rilevamento delle modifiche e questo era il comportamento di eliminazione predefinito per le relazioni facoltative. In EF Core 2.0 è stato introdotto il valore *ClientSetNull* per rappresentare tale comportamento ed è diventato il valore predefinito per le relazioni facoltative. Il comportamento di *Restrict* è stato modificato in modo da non produrre mai effetti collaterali sulle entità dipendenti.

## <a name="entity-deletion-examples"></a>Esempi di eliminazione di entità

Il codice seguente fa parte di un [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) che può essere scaricato ed eseguito. L'esempio mostra cosa accade per ogni comportamento di eliminazione sia per le relazioni obbligatorie che facoltative, quando viene eliminata un'entità padre.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

Ogni variazione verrà analizzata per capire il funzionamento.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade con relazione obbligatoria o facoltativa

```console
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

* Il blog viene contrassegnato come eliminato (Deleted)
* I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges
* SaveChanges invia eliminazioni per entrambe le entità dipendenti/figlio (post) e poi per l'entità principale/padre (blog)
* Dopo il salvataggio, tutte le entità vengono scollegate perché a questo punto sono state eliminate dal database

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione obbligatoria

``` output
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

* Il blog viene contrassegnato come eliminato (Deleted)
* I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges
* SaveChanges tenta di impostare la chiave esterna del post su Null, ma l'operazione non riesce perché la chiave esterna non ammette valori Null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione facoltativa

``` output
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

* Il blog viene contrassegnato come eliminato (Deleted)
* I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges
* SaveChanges tenta di impostare la chiave esterna di entrambe le entità dipendenti/figlio (post) su Null prima di eliminare l'entità principale/padre (blog)
* Dopo il salvataggio, l'entità principale/padre (blog) viene eliminata, ma le entità dipendenti/figlio (post) sono ancora incluse nel rilevamento delle modifiche
* Le entità dipendenti/figlio (post) incluse nel rilevamento delle modifiche hanno ora valori di chiave esterna Null e il riferimento all'entità principale/padre (blog) è stato rimosso

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict con relazione obbligatoria o facoltativa

``` output
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

* Il blog viene contrassegnato come eliminato (Deleted)
* I post rimangono invariati (Unchanged) dato che le operazioni a catena non vengono eseguite fino a SaveChanges
* Poiché *Restrict* indica a EF di non impostare automaticamente la chiave esterna su Null, la chiave rimane non Null e SaveChanges genera un'eccezione senza salvare le modifiche

## <a name="delete-orphans-examples"></a>Esempi di eliminazione degli orfani

Il codice seguente fa parte di un [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) che può essere scaricato ed eseguito. L'esempio illustra le conseguenze di ogni comportamento di eliminazione sia per le relazioni facoltative che per quelle obbligatorie, quando viene interrotta la relazione tra un'entità padre/principale e le relative entità figlio/dipendenti. In questo esempio, la relazione viene interrotta rimuovendo gli oggetti dipendenti o elementi figlio (POST) dalla proprietà di navigazione della raccolta nell'entità principale/padre (blog). Tuttavia, il comportamento è lo stesso se il riferimento da entità dipendente/figlio a entità principale/padre viene invece impostato su Null.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

Ogni variazione verrà analizzata per capire il funzionamento.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade con relazione obbligatoria o facoltativa

``` output
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

* I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null
  * Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null
* SaveChanges invia eliminazioni per le entità dipendenti/figlio (post)
* Dopo il salvataggio, le entità dipendenti/figlio (post) vengono scollegate perché a questo punto sono state eliminate dal database

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione obbligatoria

``` output
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

* I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null
  * Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null
* SaveChanges tenta di impostare la chiave esterna del post su Null, ma l'operazione non riesce perché la chiave esterna non ammette valori Null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relazione facoltativa

``` output
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

* I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null
  * Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null
* SaveChanges imposta la chiave esterna di entrambe le entità dipendenti/figlio (post) su Null
* Dopo il salvataggio, le entità dipendenti/figlio (post) hanno ora valori di chiave esterna Null e il riferimento all'entità principale/padre (blog) è stato rimosso

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict con relazione obbligatoria o facoltativa

``` output
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

* I post vengono contrassegnati come modificati perché, a causa dell'interruzione della relazione, la chiave esterna è stata contrassegnata come Null
  * Se la chiave esterna non ammette valori Null, il valore effettivo non cambierà anche se è contrassegnato come Null
* Poiché *Restrict* indica a EF di non impostare automaticamente la chiave esterna su Null, la chiave rimane non Null e SaveChanges genera un'eccezione senza salvare le modifiche

## <a name="cascading-to-untracked-entities"></a>Applicazione a catena alle entità senza rilevamento delle modifiche

Quando si chiama *SaveChanges*, le regole di eliminazione a catena verranno applicate a tutte le entità incluse nel rilevamento delle modifiche dal contesto. Si tratta della situazione esistente in tutti gli esempi illustrati in precedenza, che è il motivo per cui vengono generate istruzioni SQL per eliminare sia l'entità principale/padre (blog) che tutte le entità dipendenti/figlio (post):

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Se viene caricata solo l'entità principale, ad esempio, quando viene eseguita una query per un blog senza `Include(b => b.Posts)` per includere anche i post, SaveChanges genererà solo le istruzioni SQL per eliminare l'entità principale/padre:

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Le entità dipendenti/figlio (post) verranno eliminate solo se per il database è configurato un comportamento a catena corrispondente. Se si usa EF per creare il database, questo comportamento a catena verrà configurato automaticamente.
