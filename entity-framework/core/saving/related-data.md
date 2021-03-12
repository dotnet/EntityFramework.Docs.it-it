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
# <a name="saving-related-data"></a><span data-ttu-id="6c1f3-103">Salvataggio di dati correlati</span><span class="sxs-lookup"><span data-stu-id="6c1f3-103">Saving Related Data</span></span>

<span data-ttu-id="6c1f3-104">Oltre alle entità isolate, è anche possibile usare le relazioni definite nel modello.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-104">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]
> <span data-ttu-id="6c1f3-105">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/RelatedData/) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="6c1f3-106">Aggiunta di un grafo delle nuove entità</span><span class="sxs-lookup"><span data-stu-id="6c1f3-106">Adding a graph of new entities</span></span>

<span data-ttu-id="6c1f3-107">Se si creano varie nuove entità correlate, l'aggiunta di una di esse al contesto comporterà l'aggiunta anche delle altre.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-107">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="6c1f3-108">Nell'esempio seguente il blog e i tre post correlati vengono tutti inseriti nel database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-108">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="6c1f3-109">I post vengono rilevati e aggiunti, in quanto sono raggiungibili tramite la proprietà di navigazione `Blog.Posts`.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-109">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]
> <span data-ttu-id="6c1f3-110">Usare la proprietà EntityEntry.State per impostare lo stato di una singola entità.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-110">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="6c1f3-111">Ad esempio: `context.Entry(blog).State = EntityState.Modified`.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="6c1f3-112">Aggiunta di un'entità correlata</span><span class="sxs-lookup"><span data-stu-id="6c1f3-112">Adding a related entity</span></span>

<span data-ttu-id="6c1f3-113">Se si fa riferimento a una nuova entità dalla proprietà di navigazione di un'entità già inclusa nel rilevamento delle modifiche dal contesto, l'entità verrà individuata e inserita nel database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-113">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="6c1f3-114">Nell'esempio seguente l'entità `post` viene inserita perché viene aggiunta alla proprietà `Posts` dell'entità `blog` recuperata dal database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-114">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="6c1f3-115">Modifica delle relazioni</span><span class="sxs-lookup"><span data-stu-id="6c1f3-115">Changing relationships</span></span>

<span data-ttu-id="6c1f3-116">Se si modifica la proprietà di navigazione di un'entità, le modifiche corrispondenti verranno apportate alla colonna di chiave esterna nel database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-116">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="6c1f3-117">Nell'esempio seguente l'entità `post` viene aggiornata in modo che appartenga alla nuova entità`blog` perché la relativa proprietà di navigazione `Blog` è impostata in modo da puntare a `blog`.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-117">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="6c1f3-118">Si noti che anche l'entità `blog` verrà inserita nel database perché è una nuova entità a cui fa riferimento la proprietà di navigazione di un'entità già inclusa nel rilevamento delle modifiche dal contesto (`post`).</span><span class="sxs-lookup"><span data-stu-id="6c1f3-118">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="6c1f3-119">Rimozione delle relazioni</span><span class="sxs-lookup"><span data-stu-id="6c1f3-119">Removing relationships</span></span>

<span data-ttu-id="6c1f3-120">È possibile rimuovere una relazione impostando una navigazione di riferimento su `null` oppure rimuovendo l'entità correlata dalla navigazione di una raccolta.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-120">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="6c1f3-121">La rimozione di una relazione può avere effetti collaterali sull'entità dipendente, a seconda del comportamento di eliminazione a catena configurato nella relazione.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-121">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="6c1f3-122">Per impostazione predefinita, per le relazioni obbligatorie viene configurato un comportamento di eliminazione a catena e l'entità figlio/dipendente verrà eliminata dal database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-122">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="6c1f3-123">Per le relazioni facoltative, l'eliminazione a catena non viene configurata per impostazione predefinita, ma la proprietà di chiave esterna verrà impostata su Null.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-123">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="6c1f3-124">Vedere [Relazioni obbligatorie e facoltative](xref:core/modeling/relationships#required-and-optional-relationships) per informazioni su come configurare l'obbligatorietà delle relazioni.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-124">See [Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="6c1f3-125">Vedere [Eliminazione a catena](xref:core/saving/cascade-delete) per altri dettagli su come funzionano i comportamenti di eliminazione, su come è possibile configurarli in modo esplicito e sulla modalità di selezione convenzionale.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-125">See [Cascade Delete](xref:core/saving/cascade-delete) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="6c1f3-126">Nell'esempio seguente viene configurata un'eliminazione a catena per la relazione tra `Blog` e `Post`, pertanto l'entità `post` viene eliminata dal database.</span><span class="sxs-lookup"><span data-stu-id="6c1f3-126">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
