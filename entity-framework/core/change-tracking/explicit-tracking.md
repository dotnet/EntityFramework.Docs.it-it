---
title: Rilevamento esplicito di entità-EF Core
description: Rilevamento esplicito di entità con DbContext mediante aggiunta, associazione, aggiornamento e rimozione
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 3d9142cecf272c635c3a041fe6c5d9c49a26c33d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543185"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="f8451-103">Rilevamento esplicito di entità</span><span class="sxs-lookup"><span data-stu-id="f8451-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="f8451-104">Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="f8451-105">Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="f8451-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="f8451-106">Il rilevamento delle modifiche Entity Framework Core (EF Core) funziona meglio quando la stessa <xref:Microsoft.EntityFrameworkCore.DbContext> istanza viene utilizzata per eseguire query per entità e aggiornarle chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="f8451-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="f8451-107">Questo perché EF Core rileva automaticamente lo stato delle entità sottoposte a query e quindi rileva eventuali modifiche apportate a tali entità quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f8451-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="f8451-108">Questo approccio è trattato in [Rilevamento modifiche EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="f8451-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="f8451-109">In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi.</span><span class="sxs-lookup"><span data-stu-id="f8451-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="f8451-110">Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="f8451-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="f8451-111">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="f8451-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="f8451-112">Per semplicità, in questo documento vengono usati e i riferimenti a metodi sincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> invece i relativi equivalenti asincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f8451-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="f8451-113">La chiamata e l'attesa del metodo asincrono possono essere sostituite se non diversamente specificato.</span><span class="sxs-lookup"><span data-stu-id="f8451-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="f8451-114">Introduzione</span><span class="sxs-lookup"><span data-stu-id="f8451-114">Introduction</span></span>

<span data-ttu-id="f8451-115">Le entità possono essere esplicitamente associate a un oggetto in modo <xref:Microsoft.EntityFrameworkCore.DbContext> che il contesto rilevi tali entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="f8451-116">Questa operazione è utile principalmente nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f8451-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="f8451-117">Creazione di nuove entità che verranno inserite nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="f8451-118">Ricollegare le entità disconnesse precedentemente sottoposte a query da un'istanza di DbContext _diversa_ .</span><span class="sxs-lookup"><span data-stu-id="f8451-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="f8451-119">Il primo di questi saranno necessari per la maggior parte delle applicazioni ed è primario gestito dai <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> metodi.</span><span class="sxs-lookup"><span data-stu-id="f8451-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="f8451-120">Il secondo è necessario solo per le applicazioni che modificano le entità o le relazioni **_mentre le entità non vengono rilevate_**.</span><span class="sxs-lookup"><span data-stu-id="f8451-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="f8451-121">Ad esempio, un'applicazione Web può inviare entità al client Web in cui l'utente apporta modifiche e invia le entità di nuovo.</span><span class="sxs-lookup"><span data-stu-id="f8451-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="f8451-122">Queste entità sono denominate "disconnesse" perché sono state originariamente sottoposte a query da un DbContext, ma sono state quindi disconnesse da tale contesto quando vengono inviate al client.</span><span class="sxs-lookup"><span data-stu-id="f8451-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="f8451-123">L'applicazione Web deve ora ricollegare queste entità in modo che vengano rilevate nuovamente e indichino le modifiche apportate in modo che <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> possano apportare aggiornamenti appropriati al database.</span><span class="sxs-lookup"><span data-stu-id="f8451-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="f8451-124">Questa operazione viene gestita principalmente dai <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> metodi e.</span><span class="sxs-lookup"><span data-stu-id="f8451-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="f8451-125">Il montaggio delle entità nella _stessa istanza di DbContext_ su cui sono state eseguite query non dovrebbe essere normalmente necessario.</span><span class="sxs-lookup"><span data-stu-id="f8451-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="f8451-126">Non eseguire periodicamente una query senza rilevamento e quindi alleghi le entità restituite allo stesso contesto.</span><span class="sxs-lookup"><span data-stu-id="f8451-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="f8451-127">Questa operazione risulterà più lenta rispetto all'utilizzo di una query di rilevamento e potrebbe anche causare problemi quali i valori delle proprietà shadow mancanti, rendendo più difficile ottenere il diritto.</span><span class="sxs-lookup"><span data-stu-id="f8451-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-versus-explicit-key-values"></a><span data-ttu-id="f8451-128">Valori di chiave generati rispetto a quelli espliciti</span><span class="sxs-lookup"><span data-stu-id="f8451-128">Generated versus explicit key values</span></span>

<span data-ttu-id="f8451-129">Per impostazione predefinita, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo di [valori di chiave generati automaticamente](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="f8451-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="f8451-130">Questo è un **vantaggio importante per il rilevamento delle modifiche: un valore di chiave non impostato indica che l'entità è "New"**.</span><span class="sxs-lookup"><span data-stu-id="f8451-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="f8451-131">Per "New", significa che non è stato ancora inserito nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="f8451-132">Nelle sezioni seguenti vengono usati due modelli.</span><span class="sxs-lookup"><span data-stu-id="f8451-132">Two models are used in the following sections.</span></span> <span data-ttu-id="f8451-133">Il primo è configurato in modo da **non** usare i valori di chiave generati:</span><span class="sxs-lookup"><span data-stu-id="f8451-133">The first is configured to **not** use generated key values:</span></span>

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

<span data-ttu-id="f8451-134">I valori di chiave non generati (ovvero impostati in modo esplicito) vengono visualizzati per primi in ogni esempio perché tutti gli elementi sono molto espliciti e facili da seguire.</span><span class="sxs-lookup"><span data-stu-id="f8451-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="f8451-135">Questo viene seguito da un esempio in cui vengono usati i valori di chiave generati:</span><span class="sxs-lookup"><span data-stu-id="f8451-135">This is then followed by an example where generated key values are used:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="f8451-136">Si noti che le proprietà chiave in questo modello non richiedono alcuna configurazione aggiuntiva perché l'uso di valori di chiave generati è l' [impostazione predefinita per le chiavi Integer semplici](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="f8451-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="f8451-137">Inserimento di nuove entità</span><span class="sxs-lookup"><span data-stu-id="f8451-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="f8451-138">Valori di chiave espliciti</span><span class="sxs-lookup"><span data-stu-id="f8451-138">Explicit key values</span></span>

<span data-ttu-id="f8451-139">È necessario tenere traccia di un'entità nello `Added` stato da inserire da <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="f8451-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="f8451-140">Le entità vengono in genere inserite nello stato aggiunto chiamando uno dei <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> metodi,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> o equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="f8451-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="f8451-141">Questi metodi funzionano nello stesso modo nel contesto del rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="f8451-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="f8451-142">Per ulteriori informazioni, vedere [ulteriori rilevamento modifiche funzionalità](xref:core/change-tracking/miscellaneous) .</span><span class="sxs-lookup"><span data-stu-id="f8451-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="f8451-143">Ad esempio, per iniziare a tenere traccia di un nuovo Blog:</span><span class="sxs-lookup"><span data-stu-id="f8451-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="f8451-144">Esaminando la [visualizzazione di debug di rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa chiamata viene indicato che il contesto sta monitorando la nuova entità nello `Added` stato:</span><span class="sxs-lookup"><span data-stu-id="f8451-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="f8451-145">Tuttavia, i metodi Add non funzionano solo su una singola entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="f8451-146">Iniziano effettivamente a tenere traccia _di un intero grafico di entità correlate_, inserendole tutte nello `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="f8451-147">Ad esempio, per inserire un nuovo blog e i nuovi post associati:</span><span class="sxs-lookup"><span data-stu-id="f8451-147">For example, to insert a new blog and associated new posts:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

<span data-ttu-id="f8451-148">Il contesto ora tiene traccia di tutte le entità come `Added` :</span><span class="sxs-lookup"><span data-stu-id="f8451-148">The context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-149">Si noti che sono stati impostati valori espliciti per le `Id` proprietà chiave degli esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="f8451-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="f8451-150">Questo perché il modello è stato configurato in modo da usare i valori di chiave impostati in modo esplicito, anziché i valori di chiave generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f8451-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="f8451-151">Quando non si usano chiavi generate, è necessario impostare in modo esplicito le proprietà chiave _prima_ di chiamare `Add` .</span><span class="sxs-lookup"><span data-stu-id="f8451-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="f8451-152">Questi valori di chiave vengono quindi inseriti quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f8451-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="f8451-153">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-153">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

<span data-ttu-id="f8451-154">Tutte queste entità vengono registrate nello `Unchanged` stato dopo il completamento di SaveChanges, dal momento che queste entità sono ora presenti nel database:</span><span class="sxs-lookup"><span data-stu-id="f8451-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a><span data-ttu-id="f8451-155">Valori di chiave generati</span><span class="sxs-lookup"><span data-stu-id="f8451-155">Generated key values</span></span>

<span data-ttu-id="f8451-156">Come indicato in precedenza, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo dei [valori di chiave generati automaticamente](xref:core/modeling/generated-properties) per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f8451-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="f8451-157">Ciò significa che l'applicazione _non deve impostare alcun valore di chiave in modo esplicito_.</span><span class="sxs-lookup"><span data-stu-id="f8451-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="f8451-158">Ad esempio, per inserire un nuovo blog e pubblicarli tutti con i valori di chiave generati:</span><span class="sxs-lookup"><span data-stu-id="f8451-158">For example, to insert a new blog and posts all with generated key values:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

<span data-ttu-id="f8451-159">Come per i valori di chiave espliciti, il contesto ora tiene traccia di tutte le entità come `Added` :</span><span class="sxs-lookup"><span data-stu-id="f8451-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

<span data-ttu-id="f8451-160">Si noti che in questo caso sono stati generati [valori di chiave temporanei](xref:core/change-tracking/miscellaneous#temporary-values) per ogni entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="f8451-161">Questi valori vengono utilizzati da EF Core finché non viene chiamato SaveChanges, a quel punto i valori di chiave reale vengono letti dal database.</span><span class="sxs-lookup"><span data-stu-id="f8451-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="f8451-162">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-162">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="f8451-163">Dopo il completamento di SaveChanges, tutte le entità sono state aggiornate con i relativi valori di chiave reale e vengono rilevate nello `Unchanged` stato poiché ora corrispondono allo stato nel database:</span><span class="sxs-lookup"><span data-stu-id="f8451-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-164">Si tratta esattamente dello stesso stato finale dell'esempio precedente in cui venivano utilizzati valori di chiave espliciti.</span><span class="sxs-lookup"><span data-stu-id="f8451-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="f8451-165">Un valore di chiave esplicita può comunque essere impostato anche quando si usano valori di chiave generati.</span><span class="sxs-lookup"><span data-stu-id="f8451-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="f8451-166">EF Core tenterà quindi di inserire usando questo valore di chiave.</span><span class="sxs-lookup"><span data-stu-id="f8451-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="f8451-167">Alcune configurazioni di database, tra cui SQL Server con colonne Identity, non supportano tali inserimenti e generano ([vedere questi documenti per una soluzione alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).</span><span class="sxs-lookup"><span data-stu-id="f8451-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw ([see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="f8451-168">Associazione di entità esistenti</span><span class="sxs-lookup"><span data-stu-id="f8451-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="f8451-169">Valori di chiave espliciti</span><span class="sxs-lookup"><span data-stu-id="f8451-169">Explicit key values</span></span>

<span data-ttu-id="f8451-170">Le entità restituite dalle query vengono registrate nello `Unchanged` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="f8451-171">Lo `Unchanged` stato indica che l'entità non è stata modificata dopo essere stata sottoposta a query.</span><span class="sxs-lookup"><span data-stu-id="f8451-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="f8451-172">Un'entità disconnessa, forse restituita da un client Web in una richiesta HTTP, può essere inserita in questo stato usando <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> o i metodi equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="f8451-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="f8451-173">Ad esempio, per iniziare a tenere traccia di un blog esistente:</span><span class="sxs-lookup"><span data-stu-id="f8451-173">For example, to start tracking an existing blog:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> <span data-ttu-id="f8451-174">Gli esempi qui illustrano come creare entità in modo esplicito con `new` per semplicità.</span><span class="sxs-lookup"><span data-stu-id="f8451-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="f8451-175">In genere, le istanze dell'entità proverranno da un'altra origine, ad esempio la deserializzazione da un client o la creazione dai dati in un post HTTP.</span><span class="sxs-lookup"><span data-stu-id="f8451-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="f8451-176">Il controllo della [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa chiamata indica che l'entità è registrata nello `Unchanged` stato:</span><span class="sxs-lookup"><span data-stu-id="f8451-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="f8451-177">Proprio come `Add` , `Attach` imposta effettivamente un intero grafico di entità connesse sullo `Unchanged` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="f8451-178">Ad esempio, per alleghi un blog esistente e i post esistenti associati:</span><span class="sxs-lookup"><span data-stu-id="f8451-178">For example, to attach an existing blog and associated existing posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

<span data-ttu-id="f8451-179">Il contesto ora tiene traccia di tutte le entità come `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="f8451-179">The context is now tracking all these entities as `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-180">La chiamata a SaveChanges in questo punto non avrà alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="f8451-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="f8451-181">Tutte le entità sono contrassegnate come `Unchanged` , pertanto non c'è nulla da aggiornare nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="f8451-182">Valori di chiave generati</span><span class="sxs-lookup"><span data-stu-id="f8451-182">Generated key values</span></span>

<span data-ttu-id="f8451-183">Come indicato in precedenza, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo dei [valori di chiave generati automaticamente](xref:core/modeling/generated-properties) per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f8451-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="f8451-184">Questo è un vantaggio importante quando si utilizzano le entità disconnesse: un valore di chiave non impostato indica che l'entità non è ancora stata inserita nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="f8451-185">Questo consente allo strumento di rilevamento delle modifiche di rilevare automaticamente le nuove entità e inserirle nello `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="f8451-186">Si consideri ad esempio l'associazione di questo grafico di un blog e post:</span><span class="sxs-lookup"><span data-stu-id="f8451-186">For example, consider attaching this graph of a blog and posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

<span data-ttu-id="f8451-187">Il Blog ha un valore chiave pari a 1, a indicare che esiste già nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="f8451-188">Per due dei post sono inoltre impostati i valori di chiave, ma il terzo non lo è.</span><span class="sxs-lookup"><span data-stu-id="f8451-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="f8451-189">In EF Core il valore della chiave verrà visualizzato come 0, il valore predefinito di CLR per un numero intero.</span><span class="sxs-lookup"><span data-stu-id="f8451-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="f8451-190">Il risultato è EF Core contrassegnare la nuova entità come `Added` anziché `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="f8451-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

<span data-ttu-id="f8451-191">La chiamata a SaveChanges a questo punto non esegue alcuna operazione con le `Unchanged` entità, ma inserisce la nuova entità nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="f8451-192">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="f8451-193">Il punto importante da notare è che, con i valori di chiave generati, EF Core è in grado di **distinguere automaticamente le nuove entità esistenti in un grafo disconnesso**.</span><span class="sxs-lookup"><span data-stu-id="f8451-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="f8451-194">In breve, quando si usano chiavi generate, EF Core inserisce sempre un'entità quando tale entità non dispone di un valore di chiave impostato.</span><span class="sxs-lookup"><span data-stu-id="f8451-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="f8451-195">Aggiornamento di entità esistenti</span><span class="sxs-lookup"><span data-stu-id="f8451-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="f8451-196">Valori di chiave espliciti</span><span class="sxs-lookup"><span data-stu-id="f8451-196">Explicit key values</span></span>

<span data-ttu-id="f8451-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> e i metodi equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> si comportano esattamente come i `Attach` metodi descritti in precedenza, ad eccezione del fatto che le entità vengono inserite in `Modfied` anziché nello `Unchanged` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="f8451-198">Ad esempio, per iniziare a tenere traccia di un blog esistente come `Modified` :</span><span class="sxs-lookup"><span data-stu-id="f8451-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="f8451-199">Il controllo della [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa chiamata indica che il contesto sta tenendo traccia dell'entità nello `Modified` stato:</span><span class="sxs-lookup"><span data-stu-id="f8451-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="f8451-200">Proprio come con `Add` e `Attach` , `Update` in realtà contrassegna _un intero grafico_ di entità correlate come `Modified` .</span><span class="sxs-lookup"><span data-stu-id="f8451-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="f8451-201">Ad esempio, per associare un blog esistente a un post esistente associato come `Modified` :</span><span class="sxs-lookup"><span data-stu-id="f8451-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

<span data-ttu-id="f8451-202">Il contesto ora tiene traccia di tutte le entità come `Modified` :</span><span class="sxs-lookup"><span data-stu-id="f8451-202">The context is now tracking all these entities as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-203">La chiamata a SaveChanges a questo punto provocherà l'invio degli aggiornamenti al database per tutte le entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="f8451-204">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-204">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a><span data-ttu-id="f8451-205">Valori di chiave generati</span><span class="sxs-lookup"><span data-stu-id="f8451-205">Generated key values</span></span>

<span data-ttu-id="f8451-206">Analogamente a `Attach` , i valori di chiave generati hanno lo stesso vantaggio principale per `Update` : un valore di chiave non impostata indica che l'entità è nuova e non è ancora stata inserita nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="f8451-207">Come con `Attach` , consente all'DbContext di rilevare automaticamente le nuove entità e inserirle nello `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="f8451-208">Si consideri ad esempio la chiamata `Update` a con questo grafico di un blog e post:</span><span class="sxs-lookup"><span data-stu-id="f8451-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

<span data-ttu-id="f8451-209">Come per l' `Attach` esempio, il post senza valore di chiave viene rilevato come nuovo e impostato sullo `Added` stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="f8451-210">Le altre entità sono contrassegnate come `Modified` :</span><span class="sxs-lookup"><span data-stu-id="f8451-210">The other entities are marked as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-211">La chiamata a questa fase provocherà l' `SaveChanges` invio degli aggiornamenti al database per tutte le entità esistenti, mentre viene inserita la nuova entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="f8451-212">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-212">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="f8451-213">Si tratta di un modo molto semplice per generare aggiornamenti e inserimenti da un grafo disconnesso.</span><span class="sxs-lookup"><span data-stu-id="f8451-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="f8451-214">Tuttavia, il risultato è che gli aggiornamenti o gli inserimenti vengono inviati al database per ogni proprietà di ogni entità rilevata, anche quando alcuni valori di proprietà potrebbero non essere stati modificati.</span><span class="sxs-lookup"><span data-stu-id="f8451-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="f8451-215">Non è troppo spaventato da questo; per molte applicazioni con piccoli grafici, può trattarsi di un modo semplice e pragmatico di generare gli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="f8451-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="f8451-216">Detto questo, altri modelli più complessi possono a volte causare aggiornamenti più efficienti, come descritto in [risoluzione delle identità in EF Core](xref:core/change-tracking/identity-resolution).</span><span class="sxs-lookup"><span data-stu-id="f8451-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="f8451-217">Eliminazione di entità esistenti</span><span class="sxs-lookup"><span data-stu-id="f8451-217">Deleting existing entities</span></span>

<span data-ttu-id="f8451-218">Per eliminare un'entità da SaveChanges, è necessario tenere traccia `Deleted` dello stato.</span><span class="sxs-lookup"><span data-stu-id="f8451-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="f8451-219">Le entità vengono in genere inserite nello `Deleted` stato chiamando uno dei <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> metodi, o equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="f8451-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="f8451-220">Ad esempio, per contrassegnare un post esistente come `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="f8451-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="f8451-221">Il controllo della [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa chiamata indica che il contesto sta tenendo traccia dell'entità nello `Deleted` stato:</span><span class="sxs-lookup"><span data-stu-id="f8451-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="f8451-222">Questa entità verrà eliminata quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f8451-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="f8451-223">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="f8451-224">Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="f8451-225">La visualizzazione debug è quindi vuota perché non è stata tenuta traccia delle entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="f8451-226">Eliminazione di entità dipendenti/figlio</span><span class="sxs-lookup"><span data-stu-id="f8451-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="f8451-227">L'eliminazione di entità dipendenti/figlio da un grafo è più semplice dell'eliminazione di entità/entità padre.</span><span class="sxs-lookup"><span data-stu-id="f8451-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="f8451-228">Per ulteriori informazioni, vedere la sezione successiva e [modificare le chiavi esterne e le navigazioni](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="f8451-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="f8451-229">È insolito chiamare `Remove` su un'entità creata con `new` .</span><span class="sxs-lookup"><span data-stu-id="f8451-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="f8451-230">Inoltre, a differenza `Add` `Attach` di e `Update` , non è insolito chiamare `Remove` su un'entità che non è già stata rilevata nello `Unchanged` `Modified` stato o.</span><span class="sxs-lookup"><span data-stu-id="f8451-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="f8451-231">È invece tipico tenere traccia di una singola entità o di un grafico di entità correlate e quindi chiamare `Remove` sulle entità che devono essere eliminate.</span><span class="sxs-lookup"><span data-stu-id="f8451-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="f8451-232">Questo grafico delle entità rilevate viene in genere creato da uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="f8451-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="f8451-233">Esecuzione di una query per le entità</span><span class="sxs-lookup"><span data-stu-id="f8451-233">Running a query for the entities</span></span>
2. <span data-ttu-id="f8451-234">Utilizzo dei `Attach` `Update` metodi o su un grafico di entità disconnesse, come descritto nelle sezioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="f8451-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="f8451-235">Ad esempio, il codice nella sezione precedente è più probabile che ottenga un post da un client e quindi esegua una simile operazione:</span><span class="sxs-lookup"><span data-stu-id="f8451-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="f8451-236">Questo comportamento è identico a quello dell'esempio precedente, perché la chiamata `Remove` su un'entità non rilevata ne causa la prima connessione e quindi contrassegnata come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="f8451-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="f8451-237">In esempi più realistici, un grafico delle entità viene prima collegato, quindi alcune di queste entità sono contrassegnate come eliminate.</span><span class="sxs-lookup"><span data-stu-id="f8451-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="f8451-238">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f8451-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="f8451-239">Tutte le entità sono contrassegnate come `Unchanged` , ad eccezione di quella su cui `Remove` è stato chiamato:</span><span class="sxs-lookup"><span data-stu-id="f8451-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-240">Questa entità verrà eliminata quando viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f8451-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="f8451-241">Ad esempio, quando si usa SQLite:</span><span class="sxs-lookup"><span data-stu-id="f8451-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="f8451-242">Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="f8451-243">Le altre entità rimangono nello `Unchanged` stato:</span><span class="sxs-lookup"><span data-stu-id="f8451-243">Other entities remain in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="f8451-244">Eliminazione di entità/entità padre</span><span class="sxs-lookup"><span data-stu-id="f8451-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="f8451-245">Ogni relazione che connette due tipi di entità dispone di un'entità finale o di un elemento padre e un'entità finale dipendente o figlio.</span><span class="sxs-lookup"><span data-stu-id="f8451-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="f8451-246">L'entità dipendente/figlio è quella con la proprietà della chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="f8451-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="f8451-247">In una relazione uno-a-molti, l'entità/elemento padre si trova sul lato "uno" e l'oggetto dipendente/figlio si trova sul lato "molti".</span><span class="sxs-lookup"><span data-stu-id="f8451-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="f8451-248">Per ulteriori informazioni, vedere [relazioni](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="f8451-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="f8451-249">Negli esempi precedenti è stato eliminato un post, che è un'entità dipendente/figlio nella relazione uno-a-molti dei post di Blog.</span><span class="sxs-lookup"><span data-stu-id="f8451-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="f8451-250">Questo comportamento è relativamente semplice perché la rimozione di un'entità dipendente/figlio non ha alcun effetto sulle altre entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="f8451-251">D'altra parte, l'eliminazione di un'entità principale/padre deve avere un effetto anche sulle entità dipendenti/figlio.</span><span class="sxs-lookup"><span data-stu-id="f8451-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="f8451-252">Se non si esegue questa operazione, un valore di chiave esterna farà riferimento a un valore di chiave primaria che non esiste più.</span><span class="sxs-lookup"><span data-stu-id="f8451-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="f8451-253">Si tratta di uno stato del modello non valido e viene restituito un errore di vincolo referenziale nella maggior parte dei database.</span><span class="sxs-lookup"><span data-stu-id="f8451-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="f8451-254">Questo stato del modello non valido può essere gestito in due modi:</span><span class="sxs-lookup"><span data-stu-id="f8451-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="f8451-255">Impostazione dei valori FK su null.</span><span class="sxs-lookup"><span data-stu-id="f8451-255">Setting FK values to null.</span></span> <span data-ttu-id="f8451-256">Ciò indica che i dipendenti/elementi figlio non sono più correlati ad alcuna entità/elemento padre.</span><span class="sxs-lookup"><span data-stu-id="f8451-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="f8451-257">Si tratta dell'impostazione predefinita per le relazioni facoltative in cui la chiave esterna deve ammettere i valori null.</span><span class="sxs-lookup"><span data-stu-id="f8451-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="f8451-258">L'impostazione di FK su null non è valida per le relazioni obbligatorie, in cui la chiave esterna è in genere non nullable.</span><span class="sxs-lookup"><span data-stu-id="f8451-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="f8451-259">Eliminazione dei dipendenti/figlio.</span><span class="sxs-lookup"><span data-stu-id="f8451-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="f8451-260">Si tratta dell'impostazione predefinita per le relazioni obbligatorie ed è valida anche per le relazioni facoltative.</span><span class="sxs-lookup"><span data-stu-id="f8451-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="f8451-261">Per informazioni dettagliate sul rilevamento delle modifiche e sulle relazioni, vedere [modifica delle chiavi esterne e degli spostamenti](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="f8451-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="f8451-262">Relazioni facoltative</span><span class="sxs-lookup"><span data-stu-id="f8451-262">Optional relationships</span></span>

<span data-ttu-id="f8451-263">La `Post.BlogId` proprietà di chiave esterna ammette i valori null nel modello usato.</span><span class="sxs-lookup"><span data-stu-id="f8451-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="f8451-264">Ciò significa che la relazione è facoltativa e, di conseguenza, il comportamento predefinito di EF Core consiste nell'impostare le `BlogId` proprietà di chiave esterna su null quando il Blog viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="f8451-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="f8451-265">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f8451-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="f8451-266">Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo la chiamata a `Remove` viene mostrato che, come previsto, il Blog è ora contrassegnato come `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="f8451-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

<span data-ttu-id="f8451-267">Più interessante, tutti i post correlati sono ora contrassegnati come `Modified` .</span><span class="sxs-lookup"><span data-stu-id="f8451-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="f8451-268">Questo è dovuto al fatto che la proprietà di chiave esterna in ogni entità è stata impostata su null.</span><span class="sxs-lookup"><span data-stu-id="f8451-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="f8451-269">La chiamata a SaveChanges aggiorna il valore di chiave esterna per ogni post a null nel database, prima di eliminare il Blog:</span><span class="sxs-lookup"><span data-stu-id="f8451-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

<span data-ttu-id="f8451-270">Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="f8451-271">Le altre entità sono ora contrassegnate `Unchanged` con valori di chiave esterna null, che corrispondono allo stato del database:</span><span class="sxs-lookup"><span data-stu-id="f8451-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a><span data-ttu-id="f8451-272">Relazioni obbligatorie</span><span class="sxs-lookup"><span data-stu-id="f8451-272">Required relationships</span></span>

<span data-ttu-id="f8451-273">Se la `Post.BlogId` proprietà della chiave esterna è non nullable, la relazione tra Blog e post diventa "obbligatoria".</span><span class="sxs-lookup"><span data-stu-id="f8451-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="f8451-274">In questa situazione, per impostazione predefinita, EF Core eliminerà le entità dipendenti/figlio quando l'entità o il padre viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="f8451-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="f8451-275">Ad esempio, l'eliminazione di un Blog con i post correlati come nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="f8451-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="f8451-276">Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo la chiamata a `Remove` viene mostrato che, come previsto, il Blog è di nuovo contrassegnato come `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="f8451-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f8451-277">Più interessante, in questo caso, tutti i post correlati sono stati contrassegnati come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="f8451-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="f8451-278">La chiamata a SaveChanges comporta l'eliminazione del Blog e di tutti i post correlati dal database:</span><span class="sxs-lookup"><span data-stu-id="f8451-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

<span data-ttu-id="f8451-279">Dopo il completamento di SaveChanges, tutte le entità eliminate vengono scollegate dal DbContext poiché non esistono più nel database.</span><span class="sxs-lookup"><span data-stu-id="f8451-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="f8451-280">L'output della visualizzazione debug è quindi vuoto.</span><span class="sxs-lookup"><span data-stu-id="f8451-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="f8451-281">Questo documento graffia solo la superficie di utilizzo delle relazioni in EF Core.</span><span class="sxs-lookup"><span data-stu-id="f8451-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="f8451-282">Per ulteriori informazioni sulla modellazione delle relazioni e la [modifica delle chiavi esterne e](xref:core/change-tracking/relationship-changes) delle esplorazioni, vedere [relazioni](xref:core/modeling/relationships) per ulteriori informazioni sull'aggiornamento o l'eliminazione di entità dipendenti/figlio quando si chiama SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f8451-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="f8451-283">Rilevamento personalizzato con TrackGraph</span><span class="sxs-lookup"><span data-stu-id="f8451-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="f8451-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funziona come `Add` `Attach` e, `Update` ad eccezione del fatto che genera un callback per ogni istanza di entità prima di tenerne traccia.</span><span class="sxs-lookup"><span data-stu-id="f8451-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="f8451-285">In questo modo è possibile usare la logica personalizzata per determinare come tenere traccia delle singole entità in un grafico.</span><span class="sxs-lookup"><span data-stu-id="f8451-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="f8451-286">Si consideri, ad esempio, la regola utilizzata da EF Core per tenere traccia delle entità con valori di chiave generati: se il valore della chiave è zero, l'entità è nuova e deve essere inserita.</span><span class="sxs-lookup"><span data-stu-id="f8451-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the key value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="f8451-287">Si estenderà questa regola per indicare se il valore della chiave è negativo, quindi l'entità deve essere eliminata.</span><span class="sxs-lookup"><span data-stu-id="f8451-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="f8451-288">In questo modo è possibile modificare i valori di chiave primaria nelle entità di un grafo disconnesso per contrassegnare le entità eliminate:</span><span class="sxs-lookup"><span data-stu-id="f8451-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

<span data-ttu-id="f8451-289">Il grafo disconnesso può quindi essere rilevato usando TrackGraph:</span><span class="sxs-lookup"><span data-stu-id="f8451-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

<span data-ttu-id="f8451-290">Per ogni entità nel grafico, il codice sopra riportato controlla il valore della chiave primaria _prima di tenere traccia dell'entità_.</span><span class="sxs-lookup"><span data-stu-id="f8451-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="f8451-291">Per i valori di chiave non impostati (zero), il codice esegue le operazioni EF Core normalmente.</span><span class="sxs-lookup"><span data-stu-id="f8451-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="f8451-292">Ovvero, se la chiave non è impostata, l'entità è contrassegnata come `Added` .</span><span class="sxs-lookup"><span data-stu-id="f8451-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="f8451-293">Se la chiave è impostata e il valore è non negativo, l'entità è contrassegnata come `Modified` .</span><span class="sxs-lookup"><span data-stu-id="f8451-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="f8451-294">Tuttavia, se viene trovato un valore di chiave negativo, il relativo valore reale non negativo viene ripristinato e l'entità viene rilevata come `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="f8451-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="f8451-295">L'output dell'esecuzione del codice è:</span><span class="sxs-lookup"><span data-stu-id="f8451-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="f8451-296">Per semplicità, questo codice presuppone che ogni entità disponga di una proprietà di chiave primaria integer denominata `Id` .</span><span class="sxs-lookup"><span data-stu-id="f8451-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="f8451-297">Questo può essere codificato in una classe di base astratta o in un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="f8451-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="f8451-298">In alternativa, è possibile ottenere la proprietà o le proprietà della chiave primaria dai <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati in modo che questo codice funzioni con qualsiasi tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="f8451-299">TrackGraph ha due overload.</span><span class="sxs-lookup"><span data-stu-id="f8451-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="f8451-300">Nell'overload semplice utilizzato sopra, EF Core determina quando arrestare l'attraversamento del grafo.</span><span class="sxs-lookup"><span data-stu-id="f8451-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="f8451-301">In particolare, viene arrestata la visita di nuove entità correlate da un'entità specificata quando tale entità è già rilevata o quando il callback non avvia il rilevamento dell'entità.</span><span class="sxs-lookup"><span data-stu-id="f8451-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="f8451-302">L'overload avanzato, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , dispone di un callback che restituisce un valore bool.</span><span class="sxs-lookup"><span data-stu-id="f8451-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="f8451-303">Se il callback restituisce false, l'attraversamento del grafo si interrompe, in caso contrario continua.</span><span class="sxs-lookup"><span data-stu-id="f8451-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="f8451-304">È necessario prestare attenzione per evitare cicli infiniti quando si utilizza questo overload.</span><span class="sxs-lookup"><span data-stu-id="f8451-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="f8451-305">L'overload avanzato consente inoltre di fornire lo stato a TrackGraph e questo stato viene quindi passato a ogni callback.</span><span class="sxs-lookup"><span data-stu-id="f8451-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>
