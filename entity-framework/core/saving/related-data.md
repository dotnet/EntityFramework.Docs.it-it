---
title: Salvataggio di dati correlati - EF Core
description: Informazioni sul salvataggio di grafici di entità correlate e sulla gestione delle relazioni in Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/related-data
ms.openlocfilehash: abb8c0ddd146f38935753a5883dbbf3c1c40e3c9
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023471"
---
# <a name="saving-related-data"></a>Salvataggio di dati correlati

Oltre alle entità isolate, è anche possibile usare le relazioni definite nel modello.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/RelatedData/) di questo articolo in GitHub.

## <a name="adding-a-graph-of-new-entities"></a>Aggiunta di un grafo delle nuove entità

Se si creano varie nuove entità correlate, l'aggiunta di una di esse al contesto comporterà l'aggiunta anche delle altre.

Nell'esempio seguente il blog e i tre post correlati vengono tutti inseriti nel database. I post vengono rilevati e aggiunti, in quanto sono raggiungibili tramite la proprietà di navigazione `Blog.Posts`.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]
> Usare la proprietà EntityEntry.State per impostare lo stato di una singola entità. Ad esempio: `context.Entry(blog).State = EntityState.Modified`.

## <a name="adding-a-related-entity"></a>Aggiunta di un'entità correlata

Se si fa riferimento a una nuova entità dalla proprietà di navigazione di un'entità già inclusa nel rilevamento delle modifiche dal contesto, l'entità verrà individuata e inserita nel database.

Nell'esempio seguente l'entità `post` viene inserita perché viene aggiunta alla proprietà `Posts` dell'entità `blog` recuperata dal database.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>Modifica delle relazioni

Se si modifica la proprietà di navigazione di un'entità, le modifiche corrispondenti verranno apportate alla colonna di chiave esterna nel database.

Nell'esempio seguente l'entità `post` viene aggiornata in modo che appartenga alla nuova entità`blog` perché la relativa proprietà di navigazione `Blog` è impostata in modo da puntare a `blog`. Si noti che anche l'entità `blog` verrà inserita nel database perché è una nuova entità a cui fa riferimento la proprietà di navigazione di un'entità già inclusa nel rilevamento delle modifiche dal contesto (`post`).

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>Rimozione delle relazioni

È possibile rimuovere una relazione impostando una navigazione di riferimento su `null` oppure rimuovendo l'entità correlata dalla navigazione di una raccolta.

La rimozione di una relazione può avere effetti collaterali sull'entità dipendente, a seconda del comportamento di eliminazione a catena configurato nella relazione.

Per impostazione predefinita, per le relazioni obbligatorie viene configurato un comportamento di eliminazione a catena e l'entità figlio/dipendente verrà eliminata dal database. Per le relazioni facoltative, l'eliminazione a catena non viene configurata per impostazione predefinita, ma la proprietà di chiave esterna verrà impostata su Null.

Vedere [Relazioni obbligatorie e facoltative](xref:core/modeling/relationships#required-and-optional-relationships) per informazioni su come configurare l'obbligatorietà delle relazioni.

Vedere [Eliminazione a catena](xref:core/saving/cascade-delete) per altri dettagli su come funzionano i comportamenti di eliminazione, su come è possibile configurarli in modo esplicito e sulla modalità di selezione convenzionale.

Nell'esempio seguente viene configurata un'eliminazione a catena per la relazione tra `Blog` e `Post`, pertanto l'entità `post` viene eliminata dal database.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
