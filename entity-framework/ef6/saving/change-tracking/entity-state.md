---
title: Utilizzo degli Stati dell'entità-EF6
description: Utilizzo degli Stati dell'entità in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 981bdbca982403338f3f65a41f601641d59d74d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619977"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="9a2ca-103">Utilizzo degli Stati dell'entità</span><span class="sxs-lookup"><span data-stu-id="9a2ca-103">Working with entity states</span></span>
<span data-ttu-id="9a2ca-104">In questo argomento viene illustrato come aggiungere e alleghire entità a un contesto e come Entity Framework li elabora durante SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-104">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="9a2ca-105">Entity Framework tiene traccia dello stato delle entità mentre sono connesse a un contesto, ma in scenari disconnessi o a più livelli è possibile consentire a EF di conoscere lo stato delle entità.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-105">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="9a2ca-106">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="9a2ca-107">Stati di entità e SaveChanges</span><span class="sxs-lookup"><span data-stu-id="9a2ca-107">Entity states and SaveChanges</span></span>

<span data-ttu-id="9a2ca-108">Un'entità può essere in uno dei cinque stati in base a quanto definito dall'enumerazione EntityState.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-108">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="9a2ca-109">Questi stati sono:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-109">These states are:</span></span>  

- <span data-ttu-id="9a2ca-110">Aggiunta: l'entità è stata rilevata dal contesto ma non esiste ancora nel database</span><span class="sxs-lookup"><span data-stu-id="9a2ca-110">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="9a2ca-111">Non modificato: l'entità viene rilevata dal contesto ed esiste nel database e i relativi valori delle proprietà non sono stati modificati rispetto ai valori nel database</span><span class="sxs-lookup"><span data-stu-id="9a2ca-111">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="9a2ca-112">Modificato: l'entità viene rilevata dal contesto ed esiste nel database e alcuni o tutti i valori delle relative proprietà sono stati modificati</span><span class="sxs-lookup"><span data-stu-id="9a2ca-112">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="9a2ca-113">Deleted: l'entità viene rilevata dal contesto ed è presente nel database, ma è stata contrassegnata per l'eliminazione dal database alla successiva chiamata a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="9a2ca-113">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="9a2ca-114">Scollegato: l'entità non viene rilevata dal contesto</span><span class="sxs-lookup"><span data-stu-id="9a2ca-114">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="9a2ca-115">SaveChanges esegue diverse operazioni per le entità in Stati diversi:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-115">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="9a2ca-116">Le entità non modificate non vengono interessate da SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-116">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="9a2ca-117">Gli aggiornamenti non vengono inviati al database per le entità nello stato non modificato.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-117">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="9a2ca-118">Le entità aggiunte vengono inserite nel database e quindi diventano invariate quando viene restituito SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-118">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="9a2ca-119">Le entità modificate vengono aggiornate nel database e quindi diventano invariate quando viene restituito SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-119">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="9a2ca-120">Le entità eliminate vengono eliminate dal database e quindi scollegate dal contesto.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-120">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="9a2ca-121">Negli esempi seguenti vengono illustrati i modi in cui è possibile modificare lo stato di un'entità o di un grafico di entità.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-121">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="9a2ca-122">Aggiunta di una nuova entità al contesto</span><span class="sxs-lookup"><span data-stu-id="9a2ca-122">Adding a new entity to the context</span></span>  

<span data-ttu-id="9a2ca-123">È possibile aggiungere una nuova entità al contesto chiamando il metodo Add su DbSet.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-123">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="9a2ca-124">In questo modo l'entità viene inserita nello stato aggiunto, ovvero viene inserita nel database alla successiva chiamata a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-124">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="9a2ca-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-126">Un altro modo per aggiungere una nuova entità al contesto consiste nel modificarne lo stato in added.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-126">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="9a2ca-127">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-128">Infine, è possibile aggiungere una nuova entità al contesto mediante l'associazione a un'altra entità già rilevata.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-128">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="9a2ca-129">È possibile aggiungere la nuova entità alla proprietà di navigazione della raccolta di un'altra entità o impostando una proprietà di navigazione di riferimento di un'altra entità in modo che punti alla nuova entità.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-129">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="9a2ca-130">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-131">Si noti che per tutti questi esempi, se l'entità aggiunta presenta riferimenti ad altre entità non ancora rilevate, anche queste nuove entità verranno aggiunte al contesto e verranno inserite nel database alla successiva chiamata di SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-131">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="9a2ca-132">Associazione di un'entità esistente al contesto</span><span class="sxs-lookup"><span data-stu-id="9a2ca-132">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="9a2ca-133">Se si dispone di un'entità che è già presente nel database ma che attualmente non è stata rilevata dal contesto, è possibile indicare al contesto di tenere traccia dell'entità utilizzando il metodo di connessione su DbSet.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-133">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="9a2ca-134">L'entità si troverà nello stato non modificato nel contesto.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-134">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="9a2ca-135">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-135">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-136">Si noti che non verrà apportata alcuna modifica al database se SaveChanges viene chiamato senza eseguire altre modifiche dell'entità associata.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-136">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="9a2ca-137">Questo è dovuto al fatto che l'entità si trova nello stato non modificato.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-137">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="9a2ca-138">Un altro modo per alleghire un'entità esistente al contesto consiste nel modificarne lo stato su Unchanged.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-138">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="9a2ca-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-139">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-140">Si noti che per entrambi questi esempi, se l'entità da collegare presenta riferimenti ad altre entità non ancora rilevate, anche queste nuove entità verranno associate al contesto nello stato Unchanged.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-140">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="9a2ca-141">Associazione di un'entità esistente ma modificata al contesto</span><span class="sxs-lookup"><span data-stu-id="9a2ca-141">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="9a2ca-142">Se si dispone di un'entità che è già presente nel database, ma in cui è possibile che siano state apportate modifiche, è possibile indicare al contesto di alleghire l'entità e impostarne lo stato su Modified.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-142">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="9a2ca-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-143">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-144">Quando si modifica lo stato in modificato, tutte le proprietà dell'entità verranno contrassegnate come modificate e tutti i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-144">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="9a2ca-145">Si noti che se l'entità da collegare presenta riferimenti ad altre entità non ancora rilevate, queste nuove entità verranno associate al contesto nello stato non modificato, ma non verranno apportate automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-145">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="9a2ca-146">Se sono presenti più entità che devono essere contrassegnate come modificate, è necessario impostare lo stato per ognuna di queste entità singolarmente.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-146">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="9a2ca-147">Modifica dello stato di un'entità rilevata</span><span class="sxs-lookup"><span data-stu-id="9a2ca-147">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="9a2ca-148">È possibile modificare lo stato di un'entità già rilevata impostando la proprietà state sulla relativa voce.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-148">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="9a2ca-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-149">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9a2ca-150">Si noti che per modificare lo stato dell'entità è inoltre possibile utilizzare la chiamata a Add o Connetti per un'entità già rilevata.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-150">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="9a2ca-151">Ad esempio, se si chiama Connetti per un'entità attualmente nello stato aggiunto, il relativo stato verrà modificato in invariato.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-151">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="9a2ca-152">Modello di inserimento o aggiornamento</span><span class="sxs-lookup"><span data-stu-id="9a2ca-152">Insert or update pattern</span></span>  

<span data-ttu-id="9a2ca-153">Un modello comune per alcune applicazioni consiste nell'aggiungere un'entità come nuova (con conseguente inserimento di un database) o nel collegare un'entità come esistente e contrassegnarla come modificata (causando un aggiornamento del database), a seconda del valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-153">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="9a2ca-154">Ad esempio, quando si usano chiavi primarie Integer generate dal database, è comune considerare un'entità con una chiave zero come nuova e un'entità con una chiave diversa da zero come esistente.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-154">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="9a2ca-155">Questo modello può essere effettuato impostando lo stato dell'entità in base a un controllo del valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-155">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="9a2ca-156">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a2ca-156">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="9a2ca-157">Si noti che quando si modifica lo stato in modificato, tutte le proprietà dell'entità verranno contrassegnate come modificate e tutti i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9a2ca-157">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
