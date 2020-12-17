---
title: Uso dei proxy-EF6
description: Uso dei proxy in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635848"
---
# <a name="working-with-proxies"></a>Uso dei proxy
Quando si creano istanze di tipi di entità POCO, Entity Framework spesso crea istanze di un tipo derivato generato in modo dinamico che funge da proxy per l'entità. Questo proxy esegue l'override di alcune proprietà virtuali dell'entità per inserire hook per eseguire automaticamente le azioni quando si accede alla proprietà. Questo meccanismo, ad esempio, viene utilizzato per supportare il caricamento lazy delle relazioni. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

## <a name="disabling-proxy-creation"></a>Disabilitazione della creazione del proxy  

A volte è utile impedire a Entity Framework di creare istanze proxy. Ad esempio, la serializzazione di istanze non proxy è molto più semplice rispetto alla serializzazione delle istanze proxy. La creazione del proxy può essere disattivata cancellando il `ProxyCreationEnabled` flag. Questa operazione può essere eseguita nel costruttore del contesto. Ad esempio:  

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

Si noti che Entity Framework non creerà proxy per i tipi in cui non è presente alcun elemento per il proxy. Ciò significa che è anche possibile evitare proxy con tipi sealed e/o senza proprietà virtuali.  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>Creazione esplicita di un'istanza di un proxy  

Se si crea un'istanza di un'entità usando l'operatore New, non verrà creata un'istanza del proxy. Questo potrebbe non essere un problema, ma se è necessario creare un'istanza proxy (ad esempio, in modo che il caricamento lazy o il rilevamento delle modifiche del proxy funzioni), è possibile eseguire questa operazione utilizzando il `Create` metodo di `DbSet` . Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

La versione generica di `Create` può essere utilizzata se si desidera creare un'istanza di un tipo di entità derivato. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Si noti che il `Create` metodo non aggiunge né alleghi l'entità creata al contesto.  

Si noti che il `Create` metodo creerà solo un'istanza del tipo di entità se la creazione di un tipo di proxy per l'entità non avrà alcun valore perché non eseguirà alcuna operazione. Se, ad esempio, il tipo di entità è sealed e/o non ha proprietà virtuali, creerà `Create` solo un'istanza del tipo di entità.  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>Recupero del tipo di entità effettivo da un tipo di proxy  

I nomi dei tipi proxy hanno un aspetto simile al seguente:  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

È possibile trovare il tipo di entità per questo tipo di proxy usando il `GetObjectType` metodo da `ObjectContext` . Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

Si noti che se il tipo passato a `GetObjectType` è un'istanza di un tipo di entità che non è un tipo proxy, il tipo di entità viene ancora restituito. Ciò significa che è sempre possibile utilizzare questo metodo per ottenere il tipo di entità effettivo senza altre verifiche per verificare se il tipo è un tipo di proxy.  
