---
title: Eliminazione a catena - EF Core
description: Configurazione dei comportamenti di propagazione attivati quando un'entità viene eliminata o interrotta dal rispettivo/principale
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 6e897be6b4f5e6550d9ed3590445df60bef4fb3c
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023614"
---
# <a name="cascade-delete"></a><span data-ttu-id="875eb-103">Eliminazione a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-103">Cascade Delete</span></span>

<span data-ttu-id="875eb-104">Entity Framework Core (EF Core) rappresenta le relazioni utilizzando chiavi esterne.</span><span class="sxs-lookup"><span data-stu-id="875eb-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="875eb-105">Un'entità con una chiave esterna è l'entità figlio o dipendente nella relazione.</span><span class="sxs-lookup"><span data-stu-id="875eb-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="875eb-106">Il valore di chiave esterna di questa entità deve corrispondere al valore di chiave primaria o a un valore di chiave alternativo dell'entità correlata/padre.</span><span class="sxs-lookup"><span data-stu-id="875eb-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="875eb-107">Se l'entità principale/padre viene eliminata, i valori di chiave esterna dei dipendenti/figli non corrisponderanno più alla chiave primaria o alternativa di _qualsiasi_ entità/elemento padre.</span><span class="sxs-lookup"><span data-stu-id="875eb-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="875eb-108">Si tratta di uno stato non valido e causerà una violazione del vincolo referenziale nella maggior parte dei database.</span><span class="sxs-lookup"><span data-stu-id="875eb-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="875eb-109">Sono disponibili due opzioni per evitare la violazione del vincolo referenziale:</span><span class="sxs-lookup"><span data-stu-id="875eb-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="875eb-110">Impostare i valori FK su null</span><span class="sxs-lookup"><span data-stu-id="875eb-110">Set the FK values to null</span></span>
2. <span data-ttu-id="875eb-111">Elimina anche le entità dipendenti/figlio</span><span class="sxs-lookup"><span data-stu-id="875eb-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="875eb-112">La prima opzione è valida solo per le relazioni facoltative in cui la proprietà di chiave esterna (e la colonna del database a cui è mappata) deve ammettere i valori null.</span><span class="sxs-lookup"><span data-stu-id="875eb-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="875eb-113">La seconda opzione è valida per qualsiasi tipo di relazione ed è nota come "Cascade Delete".</span><span class="sxs-lookup"><span data-stu-id="875eb-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="875eb-114">Questo documento descrive le eliminazioni a catena (ed Elimina gli orfani) dal punto di vista dell'aggiornamento del database.</span><span class="sxs-lookup"><span data-stu-id="875eb-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="875eb-115">Viene utilizzato in modo intensivo i concetti introdotti in [rilevamento modifiche in EF Core](xref:core/change-tracking/index) e la [modifica di chiavi esterne e spostamenti](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="875eb-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="875eb-116">Assicurarsi di comprendere completamente questi concetti prima di affrontare il materiale qui.</span><span class="sxs-lookup"><span data-stu-id="875eb-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="875eb-117">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).</span><span class="sxs-lookup"><span data-stu-id="875eb-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="875eb-118">Quando si verificano i comportamenti a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-118">When cascading behaviors happen</span></span>

<span data-ttu-id="875eb-119">Le eliminazioni a catena sono necessarie quando un'entità dipendente/figlio non può più essere associata al relativo elemento principale/padre corrente.</span><span class="sxs-lookup"><span data-stu-id="875eb-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="875eb-120">Questo problema può verificarsi perché l'entità o il padre viene eliminato oppure può verificarsi quando l'entità o il padre esiste ancora, ma il dipendente/figlio non è più associato ad esso.</span><span class="sxs-lookup"><span data-stu-id="875eb-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="875eb-121">Eliminazione di un'entità/elemento padre</span><span class="sxs-lookup"><span data-stu-id="875eb-121">Deleting a principal/parent</span></span>

<span data-ttu-id="875eb-122">Si consideri questo modello semplice `Blog` in cui è il principale/padre in una relazione con `Post` , che è l'oggetto dipendente/figlio.</span><span class="sxs-lookup"><span data-stu-id="875eb-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="875eb-123">`Post.BlogId` è una proprietà di chiave esterna, il cui valore deve corrispondere alla `Post.Id` chiave primaria del post a cui appartiene il Blog.</span><span class="sxs-lookup"><span data-stu-id="875eb-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="875eb-124">Per convenzione, questa relazione è configurata come obbligatoria, perché la `Post.BlogId` proprietà della chiave esterna è non nullable.</span><span class="sxs-lookup"><span data-stu-id="875eb-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="875eb-125">Per impostazione predefinita, le relazioni obbligatorie sono configurate per l'utilizzo delle eliminazioni a</span><span class="sxs-lookup"><span data-stu-id="875eb-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="875eb-126">Per ulteriori informazioni sulle relazioni di modellazione, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="875eb-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="875eb-127">Quando si elimina un Blog, tutti i post vengono eliminati a cascata.</span><span class="sxs-lookup"><span data-stu-id="875eb-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="875eb-128">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="875eb-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="875eb-129">SaveChanges genera la seguente SQL, usando SQL Server come esempio:</span><span class="sxs-lookup"><span data-stu-id="875eb-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="875eb-130">Separazione di una relazione</span><span class="sxs-lookup"><span data-stu-id="875eb-130">Severing a relationship</span></span>

<span data-ttu-id="875eb-131">Anziché eliminare il Blog, è possibile, invece, interrompere la relazione tra ogni post e il relativo Blog.</span><span class="sxs-lookup"><span data-stu-id="875eb-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="875eb-132">Questa operazione può essere eseguita impostando la navigazione `Post.Blog` di riferimento su null per ogni post:</span><span class="sxs-lookup"><span data-stu-id="875eb-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="875eb-133">La relazione può essere interrotta anche rimuovendo ogni post dalla `Blog.Posts` navigazione della raccolta:</span><span class="sxs-lookup"><span data-stu-id="875eb-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="875eb-134">In entrambi i casi il risultato è lo stesso: il Blog non viene eliminato, ma i post che non sono più associati ad alcun Blog vengono eliminati:</span><span class="sxs-lookup"><span data-stu-id="875eb-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="875eb-135">L'eliminazione di entità che non sono più associate a un'entità/dipendente è nota come "eliminazione di orfani".</span><span class="sxs-lookup"><span data-stu-id="875eb-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="875eb-136">Gli orfani a catena e eliminazione sono strettamente correlati.</span><span class="sxs-lookup"><span data-stu-id="875eb-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="875eb-137">Entrambe comportano l'eliminazione di entità dipendenti/figlio quando la relazione con l'entità/padre richiesta è grave.</span><span class="sxs-lookup"><span data-stu-id="875eb-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="875eb-138">Per l'eliminazione a catena, questo errore si verifica perché il server principale/padre viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="875eb-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="875eb-139">Per gli orfani, l'entità principale/padre continua a esistere, ma non è più correlata alle entità dipendenti/figlio.</span><span class="sxs-lookup"><span data-stu-id="875eb-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="875eb-140">Dove si verificano i comportamenti a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="875eb-141">I comportamenti a cascata possono essere applicati a:</span><span class="sxs-lookup"><span data-stu-id="875eb-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="875eb-142">Entità rilevate dall'oggetto corrente <xref:Microsoft.EntityFrameworkCore.DbContext></span><span class="sxs-lookup"><span data-stu-id="875eb-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="875eb-143">Entità nel database che non sono state caricate nel contesto</span><span class="sxs-lookup"><span data-stu-id="875eb-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="875eb-144">Eliminazione a catena di entità rilevate</span><span class="sxs-lookup"><span data-stu-id="875eb-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="875eb-145">EF Core applica sempre comportamenti di propagazione configurati alle entità registrate.</span><span class="sxs-lookup"><span data-stu-id="875eb-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="875eb-146">Ciò significa che se l'applicazione carica tutte le entità dipendenti/figlio rilevanti in DbContext, come illustrato negli esempi precedenti, i comportamenti a cascata verranno applicati correttamente indipendentemente dalla configurazione del database.</span><span class="sxs-lookup"><span data-stu-id="875eb-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="875eb-147">La tempistica esatta di quando si verificano comportamenti di propagazione delle entità rilevate può essere controllata mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="875eb-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="875eb-148">Per ulteriori informazioni, vedere [modifica delle chiavi esterne e degli spostamenti](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="875eb-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="875eb-149">Eliminazione a catena nel database</span><span class="sxs-lookup"><span data-stu-id="875eb-149">Cascade delete in the database</span></span>

<span data-ttu-id="875eb-150">Molti sistemi di database offrono inoltre comportamenti a cascata che vengono attivati quando un'entità viene eliminata nel database.</span><span class="sxs-lookup"><span data-stu-id="875eb-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="875eb-151">EF Core configura questi comportamenti in base al comportamento di eliminazione a catena nel modello di EF Core quando viene creato un database tramite <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> o EF Core le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="875eb-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="875eb-152">Se ad esempio si usa il modello precedente, viene creata la tabella seguente per i post quando si usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="875eb-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="875eb-153">Si noti che il vincolo FOREIGN KEY che definisce la relazione tra Blog e post è configurato con `ON DELETE CASCADE` .</span><span class="sxs-lookup"><span data-stu-id="875eb-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="875eb-154">Se si è certi che il database è configurato in questo modo, è possibile eliminare un Blog _senza prima caricare i post_ e il database si occuperà di eliminare tutti i post correlati a tale Blog.</span><span class="sxs-lookup"><span data-stu-id="875eb-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="875eb-155">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="875eb-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="875eb-156">Si noti che non esiste alcuna `Include` per i post, quindi non vengono caricati.</span><span class="sxs-lookup"><span data-stu-id="875eb-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="875eb-157">SaveChanges in questo caso eliminerà solo il Blog, poiché si tratta dell'unica entità rilevata:</span><span class="sxs-lookup"><span data-stu-id="875eb-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="875eb-158">Ciò genera un'eccezione se il vincolo FOREIGN KEY nel database non è configurato per le eliminazioni a catena.</span><span class="sxs-lookup"><span data-stu-id="875eb-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="875eb-159">Tuttavia, in questo caso i post vengono eliminati dal database perché sono stati configurati con al `ON DELETE CASCADE` momento della creazione.</span><span class="sxs-lookup"><span data-stu-id="875eb-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="875eb-160">I database non hanno in genere alcun modo per eliminare automaticamente gli orfani.</span><span class="sxs-lookup"><span data-stu-id="875eb-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="875eb-161">Questo è dovuto al fatto che mentre EF Core rappresenta le relazioni utilizzando le chiavi esterne e le chiavi esterne, i database dispongono solo di chiavi esterne e non di navigazione.</span><span class="sxs-lookup"><span data-stu-id="875eb-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="875eb-162">Ciò significa che in genere non è possibile eseguire il troncamento di una relazione senza caricare entrambi i lati in DbContext.</span><span class="sxs-lookup"><span data-stu-id="875eb-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="875eb-163">Il database in memoria EF Core attualmente non supporta le eliminazioni a catena nel database.</span><span class="sxs-lookup"><span data-stu-id="875eb-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="875eb-164">Non configurare l'eliminazione a catena nel database durante l'eliminazione temporanea delle entità.</span><span class="sxs-lookup"><span data-stu-id="875eb-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="875eb-165">Questo può causare l'eliminazione accidentale di entità anziché l'eliminazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="875eb-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="875eb-166">Limitazioni del database a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-166">Database cascade limitations</span></span>

<span data-ttu-id="875eb-167">Alcuni database, in particolare SQL Server, presentano limitazioni sui comportamenti Cascade che formano i cicli.</span><span class="sxs-lookup"><span data-stu-id="875eb-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="875eb-168">Si consideri, ad esempio, il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="875eb-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="875eb-169">Questo modello ha tre relazioni, tutte obbligatorie e pertanto configurate per l'eliminazione a catena per convenzione:</span><span class="sxs-lookup"><span data-stu-id="875eb-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="875eb-170">L'eliminazione di un Blog provocherà l'eliminazione a catena di tutti i post correlati</span><span class="sxs-lookup"><span data-stu-id="875eb-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="875eb-171">Se si elimina l'autore dei post, i post creati verranno eliminati a cascata</span><span class="sxs-lookup"><span data-stu-id="875eb-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="875eb-172">Eliminando il proprietario di un Blog, il blog verrà eliminato a cascata</span><span class="sxs-lookup"><span data-stu-id="875eb-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="875eb-173">Si tratta di un'operazione ragionevole (se un po' draconiane nei criteri di gestione del Blog), ma il tentativo di creare un database di SQL Server con questi Cascade configurati genera l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="875eb-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="875eb-174">Microsoft. Data. SqlClient. SqlException (0x80131904): l'introduzione del vincolo FOREIGN KEY ' FK_Posts_Person_AuthorId ' nella tabella ' post ' può causare cicli o più percorsi Cascade.</span><span class="sxs-lookup"><span data-stu-id="875eb-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="875eb-175">Specificare ON DELETE NO ACTION o ON UPDATE NO ACTION oppure modificare gli altri vincoli FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="875eb-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="875eb-176">Esistono due modi per gestire questa situazione:</span><span class="sxs-lookup"><span data-stu-id="875eb-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="875eb-177">Modificare una o più relazioni per non eliminare Cascade.</span><span class="sxs-lookup"><span data-stu-id="875eb-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="875eb-178">Configurare il database senza una o più delle eliminazioni a cascata, quindi assicurarsi che tutte le entità dipendenti siano caricate in modo che EF Core possibile eseguire il comportamento di propagazione.</span><span class="sxs-lookup"><span data-stu-id="875eb-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="875eb-179">Prendendo il primo approccio dell'esempio, è possibile rendere facoltativa la relazione di Blog-proprietario assegnando una proprietà di chiave esterna Nullable:</span><span class="sxs-lookup"><span data-stu-id="875eb-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="875eb-180">Una relazione facoltativa consente al Blog di esistere senza proprietario, il che significa che l'eliminazione a catena non verrà più configurata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="875eb-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="875eb-181">Ciò significa che non è più presente un ciclo nelle azioni di propagazione e il database può essere creato senza errori in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="875eb-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="875eb-182">Invece di adottare il secondo approccio, è possibile lasciare che la relazione di Blog-proprietario sia obbligatoria e configurata per l'eliminazione a catena, ma applicare questa configurazione solo alle entità rilevate e non al database:</span><span class="sxs-lookup"><span data-stu-id="875eb-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="875eb-183">Che cosa accade se si caricano sia una persona che il Blog di cui si è proprietari, quindi si elimina la persona?</span><span class="sxs-lookup"><span data-stu-id="875eb-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="875eb-184">EF Core eliminerà l'eliminazione del proprietario, in modo che venga eliminato anche il Blog:</span><span class="sxs-lookup"><span data-stu-id="875eb-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="875eb-185">Tuttavia, se il Blog non viene caricato quando il proprietario viene eliminato:</span><span class="sxs-lookup"><span data-stu-id="875eb-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="875eb-186">Quindi verrà generata un'eccezione a causa della violazione del vincolo di chiave esterna nel database:</span><span class="sxs-lookup"><span data-stu-id="875eb-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="875eb-187">Microsoft. Data. SqlClient. SqlException: istruzione DELETE in conflitto con il vincolo di riferimento "FK_Blogs_People_OwnerId".</span><span class="sxs-lookup"><span data-stu-id="875eb-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="875eb-188">Il conflitto si è verificato nel database "Scratch", tabella "dbo". Blog ", colonna ' OwnerId '.</span><span class="sxs-lookup"><span data-stu-id="875eb-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="875eb-189">L'istruzione è stata interrotta.</span><span class="sxs-lookup"><span data-stu-id="875eb-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="875eb-190">Valori null a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-190">Cascading nulls</span></span>

<span data-ttu-id="875eb-191">Le relazioni facoltative dispongono di proprietà di chiave esterna Nullable mappate a colonne di database Nullable.</span><span class="sxs-lookup"><span data-stu-id="875eb-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="875eb-192">Questo significa che il valore della chiave esterna può essere impostato su null quando l'entità o il padre corrente viene eliminato o è grave dal dipendente/figlio.</span><span class="sxs-lookup"><span data-stu-id="875eb-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="875eb-193">Di seguito vengono esaminati gli esempi relativi al [momento in cui si verificano i comportamenti a cascata](#when-cascading-behaviors-happen), ma questa volta con una relazione facoltativa rappresentata da una proprietà di `Post.BlogId` chiave esterna Nullable:</span><span class="sxs-lookup"><span data-stu-id="875eb-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="875eb-194">Questa proprietà di chiave esterna verrà impostata su null per ogni post quando il relativo Blog viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="875eb-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="875eb-195">Ad esempio, questo codice, che è lo stesso di prima:</span><span class="sxs-lookup"><span data-stu-id="875eb-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="875eb-196">Determinerà gli aggiornamenti del database seguenti quando viene chiamato SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="875eb-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="875eb-197">Analogamente, se la relazione è stata interrotta utilizzando uno degli esempi precedenti:</span><span class="sxs-lookup"><span data-stu-id="875eb-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="875eb-198">Oppure:</span><span class="sxs-lookup"><span data-stu-id="875eb-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="875eb-199">I post vengono quindi aggiornati con valori di chiave esterna null quando viene chiamato SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="875eb-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="875eb-200">Per ulteriori informazioni sul modo in cui EF Core gestisce le chiavi esterne e le navigazioni, vedere [modifica](xref:core/change-tracking/relationship-changes) delle chiavi esterne e delle esplorazioni.</span><span class="sxs-lookup"><span data-stu-id="875eb-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="875eb-201">La correzione di relazioni come questa è stata il comportamento predefinito di Entity Framework dalla prima versione in 2008.</span><span class="sxs-lookup"><span data-stu-id="875eb-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="875eb-202">Prima di EF Core non aveva un nome e non era possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="875eb-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="875eb-203">È ora noto come `ClientSetNull` descritto nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="875eb-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="875eb-204">I database possono anche essere configurati per la propagazione di valori null come questo quando viene eliminata un'entità o un padre in una relazione facoltativa.</span><span class="sxs-lookup"><span data-stu-id="875eb-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="875eb-205">Questa operazione è tuttavia molto meno comune rispetto all'uso di eliminazioni a catena nel database.</span><span class="sxs-lookup"><span data-stu-id="875eb-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="875eb-206">L'utilizzo delle eliminazioni a catena e dei valori null a catena nel database genera quasi sempre i cicli di relazione quando si utilizza SQL Server.</span><span class="sxs-lookup"><span data-stu-id="875eb-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="875eb-207">Per ulteriori informazioni sulla configurazione dei valori null a catena, vedere la sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="875eb-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="875eb-208">Configurazione dei comportamenti a catena</span><span class="sxs-lookup"><span data-stu-id="875eb-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="875eb-209">Assicurarsi di leggere le sezioni precedenti prima di venire qui.</span><span class="sxs-lookup"><span data-stu-id="875eb-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="875eb-210">Le opzioni di configurazione probabilmente non hanno senso se il materiale precedente non è compreso.</span><span class="sxs-lookup"><span data-stu-id="875eb-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="875eb-211">I comportamenti a cascata sono configurati per relazione usando il <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> metodo in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="875eb-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="875eb-212">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="875eb-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="875eb-213">Per ulteriori informazioni sulla configurazione delle relazioni tra tipi di entità, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="875eb-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="875eb-214">`OnDelete` accetta un valore dell'enumerazione, che si confonde <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> .</span><span class="sxs-lookup"><span data-stu-id="875eb-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="875eb-215">Questa enumerazione definisce sia il comportamento di EF Core sulle entità rilevate che la configurazione dell'eliminazione a catena nel database quando EF viene utilizzato per creare lo schema.</span><span class="sxs-lookup"><span data-stu-id="875eb-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="875eb-216">Effetto sullo schema del database</span><span class="sxs-lookup"><span data-stu-id="875eb-216">Impact on database schema</span></span>

<span data-ttu-id="875eb-217">La tabella seguente mostra il risultato di ogni `OnDelete` valore nel vincolo FOREIGN KEY creato da EF Core Migrations o <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> .</span><span class="sxs-lookup"><span data-stu-id="875eb-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="875eb-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="875eb-218">DeleteBehavior</span></span>        | <span data-ttu-id="875eb-219">Effetto sullo schema del database</span><span class="sxs-lookup"><span data-stu-id="875eb-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="875eb-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="875eb-220">Cascade</span></span>               | <span data-ttu-id="875eb-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="875eb-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="875eb-222">Limitazione</span><span class="sxs-lookup"><span data-stu-id="875eb-222">Restrict</span></span>              | <span data-ttu-id="875eb-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="875eb-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="875eb-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-224">NoAction</span></span>              | <span data-ttu-id="875eb-225">impostazione predefinita database</span><span class="sxs-lookup"><span data-stu-id="875eb-225">database default</span></span>
| <span data-ttu-id="875eb-226">SetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-226">SetNull</span></span>               | <span data-ttu-id="875eb-227">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="875eb-227">ON DELETE SET NULL</span></span>
| <span data-ttu-id="875eb-228">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-228">ClientSetNull</span></span>         | <span data-ttu-id="875eb-229">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="875eb-229">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="875eb-230">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="875eb-230">ClientCascade</span></span>         | <span data-ttu-id="875eb-231">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="875eb-231">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="875eb-232">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-232">ClientNoAction</span></span>        | <span data-ttu-id="875eb-233">impostazione predefinita database</span><span class="sxs-lookup"><span data-stu-id="875eb-233">database default</span></span>

> [!NOTE]
> <span data-ttu-id="875eb-234">Questa tabella è confusa e si prevede di rivisitarla in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="875eb-234">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="875eb-235">Vedere [#21252 problema di GitHub](https://github.com/dotnet/efcore/issues/21252).</span><span class="sxs-lookup"><span data-stu-id="875eb-235">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="875eb-236">I comportamenti di `ON DELETE NO ACTION` e `ON DELETE RESTRICT` nei database relazionali sono in genere identici o molto simili.</span><span class="sxs-lookup"><span data-stu-id="875eb-236">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="875eb-237">Nonostante ciò che `NO ACTION` può implicare, entrambe le opzioni provocano l'applicazione di vincoli referenziali.</span><span class="sxs-lookup"><span data-stu-id="875eb-237">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="875eb-238">La differenza, quando ne esiste una, è _quando_ il database controlla i vincoli.</span><span class="sxs-lookup"><span data-stu-id="875eb-238">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="875eb-239">Controllare la documentazione del database per le differenze specifiche tra `ON DELETE NO ACTION` e `ON DELETE RESTRICT` nel sistema di database.</span><span class="sxs-lookup"><span data-stu-id="875eb-239">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="875eb-240">Gli unici valori che determineranno comportamenti a catena nel database sono `Cascade` e `SetNull` .</span><span class="sxs-lookup"><span data-stu-id="875eb-240">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="875eb-241">Tutti gli altri valori configureranno il database in modo che non vengano propagate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="875eb-241">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="875eb-242">Effetti sul comportamento di SaveChanges</span><span class="sxs-lookup"><span data-stu-id="875eb-242">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="875eb-243">Le tabelle nelle sezioni seguenti illustrano cosa accade alle entità dipendenti/figlio quando l'entità o il padre viene eliminato oppure la relazione con le entità dipendenti/figlio è grave.</span><span class="sxs-lookup"><span data-stu-id="875eb-243">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="875eb-244">Ogni tabella copre uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="875eb-244">Each table covers one of:</span></span>

- <span data-ttu-id="875eb-245">Relazioni facoltative (Nullable FK) e richieste (FK non nullable)</span><span class="sxs-lookup"><span data-stu-id="875eb-245">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="875eb-246">Quando dipendenti/figli vengono caricati e monitorati da DbContext e quando esistono solo nel database</span><span class="sxs-lookup"><span data-stu-id="875eb-246">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="875eb-247">Relazione obbligatoria con dipendenti/figli caricati</span><span class="sxs-lookup"><span data-stu-id="875eb-247">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="875eb-248">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="875eb-248">DeleteBehavior</span></span>    | <span data-ttu-id="875eb-249">All'eliminazione dell'entità/elemento padre</span><span class="sxs-lookup"><span data-stu-id="875eb-249">On deleting principal/parent</span></span>             | <span data-ttu-id="875eb-250">In base al server principale/padre</span><span class="sxs-lookup"><span data-stu-id="875eb-250">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="875eb-251">Cascade</span><span class="sxs-lookup"><span data-stu-id="875eb-251">Cascade</span></span>           | <span data-ttu-id="875eb-252">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-252">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="875eb-253">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-253">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="875eb-254">Limitazione</span><span class="sxs-lookup"><span data-stu-id="875eb-254">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="875eb-255">NoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-255">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="875eb-256">SetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-256">SetNull</span></span>           | <span data-ttu-id="875eb-257">`SqlException` durante la creazione del database</span><span class="sxs-lookup"><span data-stu-id="875eb-257">`SqlException` on creating database</span></span>      | <span data-ttu-id="875eb-258">`SqlException` durante la creazione del database</span><span class="sxs-lookup"><span data-stu-id="875eb-258">`SqlException` on creating database</span></span>
| <span data-ttu-id="875eb-259">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-259">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="875eb-260">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="875eb-260">ClientCascade</span></span>     | <span data-ttu-id="875eb-261">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-261">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="875eb-262">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-262">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="875eb-263">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-263">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="875eb-264">Note:</span><span class="sxs-lookup"><span data-stu-id="875eb-264">Notes:</span></span>

- <span data-ttu-id="875eb-265">Il valore predefinito per le relazioni obbligatorie come questa è `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="875eb-265">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="875eb-266">Se si utilizza un valore diverso da Cascade Delete per le relazioni obbligatorie, verrà generata un'eccezione quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="875eb-266">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="875eb-267">In genere, si tratta di un `InvalidOperationException` EF core dal momento che lo stato non valido viene rilevato negli elementi figlio/dipendenti caricati.</span><span class="sxs-lookup"><span data-stu-id="875eb-267">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="875eb-268">`ClientNoAction` impone EF Core di non controllare i dipendenti della correzione prima di inviarli al database, in questo caso il database genera un'eccezione, che viene quindi sottoposto a wrapper in `DbUpdateException` da SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="875eb-268">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="875eb-269">`SetNull` viene rifiutato quando si crea il database perché la colonna chiave esterna non ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="875eb-269">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="875eb-270">Poiché i dipendenti/figli vengono caricati, vengono sempre eliminati da EF Core e non sono mai rimasti per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="875eb-270">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="875eb-271">Relazione obbligatoria con dipendenti/figli non caricati</span><span class="sxs-lookup"><span data-stu-id="875eb-271">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="875eb-272">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="875eb-272">DeleteBehavior</span></span>    | <span data-ttu-id="875eb-273">All'eliminazione dell'entità/elemento padre</span><span class="sxs-lookup"><span data-stu-id="875eb-273">On deleting principal/parent</span></span>             | <span data-ttu-id="875eb-274">In base al server principale/padre</span><span class="sxs-lookup"><span data-stu-id="875eb-274">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="875eb-275">Cascade</span><span class="sxs-lookup"><span data-stu-id="875eb-275">Cascade</span></span>           | <span data-ttu-id="875eb-276">Dipendenti eliminati dal database</span><span class="sxs-lookup"><span data-stu-id="875eb-276">Dependents deleted by database</span></span>           | <span data-ttu-id="875eb-277">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-277">N/A</span></span>
| <span data-ttu-id="875eb-278">Limitazione</span><span class="sxs-lookup"><span data-stu-id="875eb-278">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="875eb-279">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-279">N/A</span></span>
| <span data-ttu-id="875eb-280">NoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-280">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="875eb-281">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-281">N/A</span></span>
| <span data-ttu-id="875eb-282">SetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-282">SetNull</span></span>           | <span data-ttu-id="875eb-283">`SqlException` durante la creazione del database</span><span class="sxs-lookup"><span data-stu-id="875eb-283">`SqlException` on creating database</span></span>      | <span data-ttu-id="875eb-284">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-284">N/A</span></span>
| <span data-ttu-id="875eb-285">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-285">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="875eb-286">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-286">N/A</span></span>
| <span data-ttu-id="875eb-287">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="875eb-287">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="875eb-288">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-288">N/A</span></span>
| <span data-ttu-id="875eb-289">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-289">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="875eb-290">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-290">N/A</span></span>

<span data-ttu-id="875eb-291">Note:</span><span class="sxs-lookup"><span data-stu-id="875eb-291">Notes:</span></span>

- <span data-ttu-id="875eb-292">Il troncamento di una relazione non è valido perché i dipendenti/figli non vengono caricati.</span><span class="sxs-lookup"><span data-stu-id="875eb-292">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="875eb-293">Il valore predefinito per le relazioni obbligatorie come questa è `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="875eb-293">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="875eb-294">Se si utilizza un valore diverso da Cascade Delete per le relazioni obbligatorie, verrà generata un'eccezione quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="875eb-294">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="875eb-295">In genere, si tratta di un oggetto `DbUpdateException` perché i dipendenti/figli non vengono caricati e pertanto lo stato non valido può essere rilevato solo dal database.</span><span class="sxs-lookup"><span data-stu-id="875eb-295">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="875eb-296">SaveChanges esegue quindi il wrapping dell'eccezione del database in un oggetto `DbUpdateException` .</span><span class="sxs-lookup"><span data-stu-id="875eb-296">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="875eb-297">`SetNull` viene rifiutato quando si crea il database perché la colonna chiave esterna non ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="875eb-297">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="875eb-298">Relazione facoltativa con dipendenti/figli caricati</span><span class="sxs-lookup"><span data-stu-id="875eb-298">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="875eb-299">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="875eb-299">DeleteBehavior</span></span>    | <span data-ttu-id="875eb-300">All'eliminazione dell'entità/elemento padre</span><span class="sxs-lookup"><span data-stu-id="875eb-300">On deleting principal/parent</span></span>             | <span data-ttu-id="875eb-301">In base al server principale/padre</span><span class="sxs-lookup"><span data-stu-id="875eb-301">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="875eb-302">Cascade</span><span class="sxs-lookup"><span data-stu-id="875eb-302">Cascade</span></span>           | <span data-ttu-id="875eb-303">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-303">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="875eb-304">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-304">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="875eb-305">Limitazione</span><span class="sxs-lookup"><span data-stu-id="875eb-305">Restrict</span></span>          | <span data-ttu-id="875eb-306">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-306">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="875eb-307">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-307">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="875eb-308">NoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-308">NoAction</span></span>          | <span data-ttu-id="875eb-309">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-309">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="875eb-310">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-310">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="875eb-311">SetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-311">SetNull</span></span>           | <span data-ttu-id="875eb-312">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-312">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="875eb-313">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-313">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="875eb-314">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-314">ClientSetNull</span></span>     | <span data-ttu-id="875eb-315">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-315">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="875eb-316">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-316">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="875eb-317">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="875eb-317">ClientCascade</span></span>     | <span data-ttu-id="875eb-318">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-318">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="875eb-319">Dipendenti eliminati da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-319">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="875eb-320">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-320">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="875eb-321">FKs dipendente impostato su null da EF Core</span><span class="sxs-lookup"><span data-stu-id="875eb-321">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="875eb-322">Note:</span><span class="sxs-lookup"><span data-stu-id="875eb-322">Notes:</span></span>

- <span data-ttu-id="875eb-323">Il valore predefinito per le relazioni facoltative come questa è `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="875eb-323">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="875eb-324">I dipendenti/figli non vengono mai eliminati, a meno che non `Cascade` `ClientCascade` siano configurati o.</span><span class="sxs-lookup"><span data-stu-id="875eb-324">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="875eb-325">Tutti gli altri valori determinano l'impostazione del FKs dipendente su null da EF Core...</span><span class="sxs-lookup"><span data-stu-id="875eb-325">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="875eb-326">... `ClientNoAction` con la differenza che indica EF Core di non toccare le chiavi esterne di dipendenti/figli quando l'entità o il padre viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="875eb-326">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="875eb-327">Il database genera quindi un'eccezione, che viene incapsulata come `DbUpdateException` da SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="875eb-327">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="875eb-328">Relazione facoltativa con dipendenti/figli non caricati</span><span class="sxs-lookup"><span data-stu-id="875eb-328">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="875eb-329">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="875eb-329">DeleteBehavior</span></span>    | <span data-ttu-id="875eb-330">All'eliminazione dell'entità/elemento padre</span><span class="sxs-lookup"><span data-stu-id="875eb-330">On deleting principal/parent</span></span>             | <span data-ttu-id="875eb-331">In base al server principale/padre</span><span class="sxs-lookup"><span data-stu-id="875eb-331">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="875eb-332">Cascade</span><span class="sxs-lookup"><span data-stu-id="875eb-332">Cascade</span></span>           | <span data-ttu-id="875eb-333">Dipendenti eliminati dal database</span><span class="sxs-lookup"><span data-stu-id="875eb-333">Dependents deleted by database</span></span>           | <span data-ttu-id="875eb-334">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-334">N/A</span></span>
| <span data-ttu-id="875eb-335">Limitazione</span><span class="sxs-lookup"><span data-stu-id="875eb-335">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="875eb-336">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-336">N/A</span></span>
| <span data-ttu-id="875eb-337">NoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-337">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="875eb-338">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-338">N/A</span></span>
| <span data-ttu-id="875eb-339">SetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-339">SetNull</span></span>           | <span data-ttu-id="875eb-340">FKs dipendente impostato su null per database</span><span class="sxs-lookup"><span data-stu-id="875eb-340">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="875eb-341">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-341">N/A</span></span>
| <span data-ttu-id="875eb-342">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="875eb-342">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="875eb-343">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-343">N/A</span></span>
| <span data-ttu-id="875eb-344">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="875eb-344">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="875eb-345">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-345">N/A</span></span>
| <span data-ttu-id="875eb-346">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="875eb-346">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="875eb-347">N/D</span><span class="sxs-lookup"><span data-stu-id="875eb-347">N/A</span></span>

<span data-ttu-id="875eb-348">Note:</span><span class="sxs-lookup"><span data-stu-id="875eb-348">Notes:</span></span>

- <span data-ttu-id="875eb-349">Il troncamento di una relazione non è valido perché i dipendenti/figli non vengono caricati.</span><span class="sxs-lookup"><span data-stu-id="875eb-349">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="875eb-350">Il valore predefinito per le relazioni facoltative come questa è `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="875eb-350">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="875eb-351">È necessario caricare dipendenti/elementi figlio per evitare un'eccezione di database, a meno che il database non sia stato configurato per le eliminazioni o i valori null.</span><span class="sxs-lookup"><span data-stu-id="875eb-351">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
