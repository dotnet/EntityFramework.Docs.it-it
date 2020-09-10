---
title: Uso dei proxy-EF6
description: Uso dei proxy in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
uid: ef6/fundamentals/proxies
ms.openlocfilehash: 26493ecf1a894a1cd421f574de38678661f324a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618367"
---
# <a name="working-with-proxies"></a><span data-ttu-id="c2d82-103">Uso dei proxy</span><span class="sxs-lookup"><span data-stu-id="c2d82-103">Working with proxies</span></span>
<span data-ttu-id="c2d82-104">Quando si creano istanze di tipi di entità POCO, Entity Framework spesso crea istanze di un tipo derivato generato in modo dinamico che funge da proxy per l'entità.</span><span class="sxs-lookup"><span data-stu-id="c2d82-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="c2d82-105">Questo proxy esegue l'override di alcune proprietà virtuali dell'entità per inserire hook per eseguire automaticamente le azioni quando si accede alla proprietà.</span><span class="sxs-lookup"><span data-stu-id="c2d82-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="c2d82-106">Questo meccanismo, ad esempio, viene utilizzato per supportare il caricamento lazy delle relazioni.</span><span class="sxs-lookup"><span data-stu-id="c2d82-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="c2d82-107">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="c2d82-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="c2d82-108">Disabilitazione della creazione del proxy</span><span class="sxs-lookup"><span data-stu-id="c2d82-108">Disabling proxy creation</span></span>  

<span data-ttu-id="c2d82-109">A volte è utile impedire a Entity Framework di creare istanze proxy.</span><span class="sxs-lookup"><span data-stu-id="c2d82-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="c2d82-110">Ad esempio, la serializzazione di istanze non proxy è molto più semplice rispetto alla serializzazione delle istanze proxy.</span><span class="sxs-lookup"><span data-stu-id="c2d82-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="c2d82-111">La creazione del proxy può essere disattivata cancellando il flag ProxyCreationEnabled.</span><span class="sxs-lookup"><span data-stu-id="c2d82-111">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="c2d82-112">Questa operazione può essere eseguita nel costruttore del contesto.</span><span class="sxs-lookup"><span data-stu-id="c2d82-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="c2d82-113">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c2d82-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="c2d82-114">Si noti che Entity Framework non creerà proxy per i tipi in cui non è presente alcun elemento per il proxy.</span><span class="sxs-lookup"><span data-stu-id="c2d82-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="c2d82-115">Ciò significa che è anche possibile evitare proxy con tipi sealed e/o senza proprietà virtuali.</span><span class="sxs-lookup"><span data-stu-id="c2d82-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="c2d82-116">Creazione esplicita di un'istanza di un proxy</span><span class="sxs-lookup"><span data-stu-id="c2d82-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="c2d82-117">Se si crea un'istanza di un'entità usando l'operatore New, non verrà creata un'istanza del proxy.</span><span class="sxs-lookup"><span data-stu-id="c2d82-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="c2d82-118">Questo potrebbe non essere un problema, ma se è necessario creare un'istanza proxy (ad esempio, in modo che il caricamento lazy o il rilevamento delle modifiche del proxy funzioni), è possibile usare il metodo create di DbSet.</span><span class="sxs-lookup"><span data-stu-id="c2d82-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="c2d82-119">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c2d82-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="c2d82-120">La versione generica di create può essere utilizzata se si desidera creare un'istanza di un tipo di entità derivato.</span><span class="sxs-lookup"><span data-stu-id="c2d82-120">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="c2d82-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c2d82-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="c2d82-122">Si noti che il metodo create non aggiunge né alleghi l'entità creata al contesto.</span><span class="sxs-lookup"><span data-stu-id="c2d82-122">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="c2d82-123">Si noti che il metodo Create creerà solo un'istanza del tipo di entità se la creazione di un tipo di proxy per l'entità non avrà alcun valore perché non eseguirà alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="c2d82-123">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="c2d82-124">Se, ad esempio, il tipo di entità è sealed e/o non contiene proprietà virtuali, create creerà solo un'istanza del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c2d82-124">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="c2d82-125">Recupero del tipo di entità effettivo da un tipo di proxy</span><span class="sxs-lookup"><span data-stu-id="c2d82-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="c2d82-126">I nomi dei tipi proxy hanno un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="c2d82-126">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="c2d82-127">System. Data. Entity. DynamicProxies. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="c2d82-127">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="c2d82-128">È possibile trovare il tipo di entità per questo tipo di proxy usando il metodo GetObjectType di ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="c2d82-128">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="c2d82-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c2d82-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="c2d82-130">Si noti che se il tipo passato a GetObjectType è un'istanza di un tipo di entità che non è un tipo proxy, il tipo di entità viene ancora restituito.</span><span class="sxs-lookup"><span data-stu-id="c2d82-130">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="c2d82-131">Ciò significa che è sempre possibile utilizzare questo metodo per ottenere il tipo di entità effettivo senza altre verifiche per verificare se il tipo è un tipo di proxy.</span><span class="sxs-lookup"><span data-stu-id="c2d82-131">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
