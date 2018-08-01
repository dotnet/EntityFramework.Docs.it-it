---
title: Utilizzo di Entity Framework 6 - stati di entità
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
caps.latest.revision: 3
ms.openlocfilehash: e27d70dd7a6a63c2b225e9cf4fe7ac8cc280d4ca
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120807"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="1d76f-102">Utilizzo di stati di entità</span><span class="sxs-lookup"><span data-stu-id="1d76f-102">Working with entity states</span></span>
<span data-ttu-id="1d76f-103">In questo argomento illustra come aggiungere e associare le entità a un contesto e le modalità di elaborazione durante SaveChanges Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1d76f-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="1d76f-104">Entity Framework si occupa di rilevamento lo stato delle entità mentre sono connessi a un contesto, ma negli scenari disconnessi o a più livelli è possibile informare EF allo stato delle entità deve essere in.</span><span class="sxs-lookup"><span data-stu-id="1d76f-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="1d76f-105">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="1d76f-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="1d76f-106">Stati di entità e salvataggio di modifiche</span><span class="sxs-lookup"><span data-stu-id="1d76f-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="1d76f-107">Un'entità può essere in uno dei cinque stati di base a quanto definito dall'enumerazione EntityState.</span><span class="sxs-lookup"><span data-stu-id="1d76f-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="1d76f-108">Questi stati sono:</span><span class="sxs-lookup"><span data-stu-id="1d76f-108">These states are:</span></span>  

- <span data-ttu-id="1d76f-109">Aggiunto: l'entità è rilevato dal contesto, ma non esiste ancora nel database</span><span class="sxs-lookup"><span data-stu-id="1d76f-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="1d76f-110">Non modificato: l'entità è rilevato dal contesto ed esiste nel database e i valori delle proprietà non modificati dai valori nel database</span><span class="sxs-lookup"><span data-stu-id="1d76f-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="1d76f-111">Modificato: l'entità è rilevato dal contesto e nel database esiste, e sono stati modificati alcuni o tutti i valori delle proprietà</span><span class="sxs-lookup"><span data-stu-id="1d76f-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="1d76f-112">Eliminato: l'entità è rilevato dal contesto e nel database esiste, ma è stato contrassegnato per l'eliminazione dal database la volta successiva che viene chiamato SaveChanges</span><span class="sxs-lookup"><span data-stu-id="1d76f-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="1d76f-113">Scollegato: l'entità non viene rilevato dal contesto</span><span class="sxs-lookup"><span data-stu-id="1d76f-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="1d76f-114">SaveChanges esegue diverse operazioni per le entità nei diversi stati:</span><span class="sxs-lookup"><span data-stu-id="1d76f-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="1d76f-115">Entità invariate vengono ignorate da SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="1d76f-116">Gli aggiornamenti non vengono inviati al database per le entità nello stato Unchanged.</span><span class="sxs-lookup"><span data-stu-id="1d76f-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="1d76f-117">Aggiungere le entità vengono inserite nel database e quindi diventano invariate quando restituisce SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="1d76f-118">Entità modificate vengono aggiornate nel database e quindi diventare invariate quando restituisce SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="1d76f-119">Le entità eliminate vengono eliminate dal database e sono quindi disconnesso dal contesto del.</span><span class="sxs-lookup"><span data-stu-id="1d76f-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="1d76f-120">Gli esempi seguenti illustrano i modi in cui è possibile modificare lo stato di un'entità o un grafico di entità.</span><span class="sxs-lookup"><span data-stu-id="1d76f-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="1d76f-121">Aggiunta di una nuova entità al contesto</span><span class="sxs-lookup"><span data-stu-id="1d76f-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="1d76f-122">Una nuova entità possono essere aggiunti al contesto chiamando il metodo Add sul DbSet.</span><span class="sxs-lookup"><span data-stu-id="1d76f-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="1d76f-123">Questo, l'entità passa allo stato Added, vale a dire che verrà inserito nel database la prossima volta che viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="1d76f-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-125">Un altro modo per aggiungere una nuova entità al contesto consiste nel modificare lo stato su Added.</span><span class="sxs-lookup"><span data-stu-id="1d76f-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="1d76f-126">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-127">Infine, è possibile aggiungere una nuova entità al contesto per eseguire l'hook fino a un'altra entità che è già registrata.</span><span class="sxs-lookup"><span data-stu-id="1d76f-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="1d76f-128">Potrebbe trattarsi di aggiungendo la nuova entità alla proprietà di navigazione dell'insieme di un'altra entità o impostando una proprietà di navigazione di riferimento di un'altra entità in modo che punti alla nuova entità.</span><span class="sxs-lookup"><span data-stu-id="1d76f-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="1d76f-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    var blog = context.Blogs.Find(2);
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-130">Si noti che per tutti questi esempi, se l'entità aggiunta dispone di riferimenti ad altre entità che non sono ancora rilevate quindi queste nuove entità verranno aggiunti al contesto e verranno inserito nel database la prossima volta che viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="1d76f-131">Collegamento di un'entità esistente nel contesto</span><span class="sxs-lookup"><span data-stu-id="1d76f-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="1d76f-132">Se si dispone di un'entità che già conosci già esiste nel database ma che non attualmente viene rilevato dal contesto è possibile impostare il contesto per tenere traccia delle entità usando il metodo Attach DbSet.</span><span class="sxs-lookup"><span data-stu-id="1d76f-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="1d76f-133">L'entità sarà nello stato Unchanged nel contesto.</span><span class="sxs-lookup"><span data-stu-id="1d76f-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="1d76f-134">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-135">Si noti che non sarà possibile apportare modifiche al database se viene chiamato SaveChanges senza eseguire eventuali altre modifiche dell'entità associata.</span><span class="sxs-lookup"><span data-stu-id="1d76f-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="1d76f-136">Questo avviene perché l'entità è nello stato Unchanged.</span><span class="sxs-lookup"><span data-stu-id="1d76f-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="1d76f-137">Un altro modo per collegare un'entità esistente nel contesto consiste nel modificare lo stato su Unchanged.</span><span class="sxs-lookup"><span data-stu-id="1d76f-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="1d76f-138">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-139">Nota che per entrambi questi esempi se l'entità che viene connesso contiene riferimenti ad altre entità che non vengono ancora rilevate quindi queste nuove entità verrà inoltre collegato nel contesto nello stato Unchanged.</span><span class="sxs-lookup"><span data-stu-id="1d76f-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="1d76f-140">Collegamento di un oggetto esistente ma entità modificata al contesto</span><span class="sxs-lookup"><span data-stu-id="1d76f-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="1d76f-141">Se si dispone di un'entità che già conosci già esiste nel database ma quali modifiche sono state rese è possibile impostare il contesto per associare l'entità e il relativo stato verrà impostato su Modified.</span><span class="sxs-lookup"><span data-stu-id="1d76f-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="1d76f-142">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d76f-143">Quando si modifica lo stato su Modified tutte le proprietà dell'entità verranno contrassegnate come modificata e i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="1d76f-144">Si noti che se l'entità che viene connesso contiene riferimenti ad altre entità che non vengono ancora rilevate, queste nuove entità verrà collegato al contesto nello stato Unchanged, essi non diventano automaticamente Modified.</span><span class="sxs-lookup"><span data-stu-id="1d76f-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="1d76f-145">Se si dispone di più entità che devono essere contrassegnati come modificati è necessario impostare lo stato singolarmente per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="1d76f-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="1d76f-146">Modifica dello stato di un'entità rilevata</span><span class="sxs-lookup"><span data-stu-id="1d76f-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="1d76f-147">È possibile modificare lo stato di un'entità che è già rilevato impostando la proprietà State nella voce corrispondente.</span><span class="sxs-lookup"><span data-stu-id="1d76f-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="1d76f-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-148">For example:</span></span>  

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

<span data-ttu-id="1d76f-149">Si noti che la chiamata a Add o Connetti per un'entità che è già registrata è anche utilizzabile per modificare lo stato dell'entità.</span><span class="sxs-lookup"><span data-stu-id="1d76f-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="1d76f-150">Ad esempio, la chiamata di connessione per un'entità che si trova attualmente nello stato Added lo stato passerà su Unchanged.</span><span class="sxs-lookup"><span data-stu-id="1d76f-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="1d76f-151">Inserire o aggiornare modello</span><span class="sxs-lookup"><span data-stu-id="1d76f-151">Insert or update pattern</span></span>  

<span data-ttu-id="1d76f-152">Un modello comune per alcune applicazioni è aggiungere un'entità come nuovo (risultante in un database di inserimento) o collegare un'entità esistente e contrassegnarla come modificata (risultante in un aggiornamento del database) in base al valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1d76f-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="1d76f-153">Ad esempio, quando si usano chiavi primarie integer generato dal database è comune a trattare un'entità con una chiave di nuovo e un'entità con una chiave diversa da zero come esistente.</span><span class="sxs-lookup"><span data-stu-id="1d76f-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="1d76f-154">Questo modello può essere ottenuto impostando lo stato dell'entità basato su un controllo del valore della chiave primario.</span><span class="sxs-lookup"><span data-stu-id="1d76f-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="1d76f-155">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1d76f-155">For example:</span></span>  

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

<span data-ttu-id="1d76f-156">Si noti che quando si modifica lo stato su Modified tutte le proprietà dell'entità verranno contrassegnate come modificata e i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1d76f-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  