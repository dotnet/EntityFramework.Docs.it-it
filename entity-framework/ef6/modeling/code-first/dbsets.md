---
title: Definizione di DbSet-EF6
description: Definizione di DbSet in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: dc5ad7f8b4ba32454c702f354b37223007e856e3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065225"
---
# <a name="defining-dbsets"></a><span data-ttu-id="bdb6e-103">Definizione di DbSet</span><span class="sxs-lookup"><span data-stu-id="bdb6e-103">Defining DbSets</span></span>
<span data-ttu-id="bdb6e-104">Quando si sviluppa con il flusso di lavoro Code First si definisce un DbContext derivato che rappresenta la sessione con il database ed espone un DbSet per ogni tipo nel modello.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="bdb6e-105">In questo argomento vengono illustrati i vari modi in cui è possibile definire le proprietà DbSet.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="bdb6e-106">DbContext con proprietà DbSet</span><span class="sxs-lookup"><span data-stu-id="bdb6e-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="bdb6e-107">Il caso comune illustrato in Code First esempi è avere un DbContext con proprietà DbSet automatiche pubbliche per i tipi di entità del modello.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="bdb6e-108">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bdb6e-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="bdb6e-109">Se usato in modalità Code First, in questo modo vengono configurati i Blog e i post come tipi di entità, nonché la configurazione di altri tipi raggiungibili da questi.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="bdb6e-110">Inoltre, DbContext chiamerà automaticamente il setter per ognuna di queste proprietà per impostare un'istanza del DbSet appropriato.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="bdb6e-111">DbContext con proprietà IDbSet</span><span class="sxs-lookup"><span data-stu-id="bdb6e-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="bdb6e-112">In alcune situazioni, ad esempio durante la creazione di simulazioni o fake, è più utile dichiarare le proprietà del set usando un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="bdb6e-113">In questi casi è possibile usare l'interfaccia IDbSet al posto di DbSet.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="bdb6e-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bdb6e-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="bdb6e-115">Questo contesto funziona esattamente allo stesso modo del contesto che usa la classe DbSet per le proprietà impostate.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="bdb6e-116">DbContext con proprietà set di sola lettura</span><span class="sxs-lookup"><span data-stu-id="bdb6e-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="bdb6e-117">Se non si vuole esporre Setter pubblici per le proprietà DbSet o IDbSet, è possibile creare le proprietà di sola lettura e creare le istanze del set manualmente.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="bdb6e-118">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bdb6e-118">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="bdb6e-119">Si noti che DbContext memorizza nella cache l'istanza di DbSet restituita dal metodo set, in modo che ognuna di queste proprietà restituisca la stessa istanza ogni volta che viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="bdb6e-120">L'individuazione dei tipi di entità per Code First funziona nello stesso modo in cui avviene per le proprietà con Getter e setter pubblici.</span><span class="sxs-lookup"><span data-stu-id="bdb6e-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
