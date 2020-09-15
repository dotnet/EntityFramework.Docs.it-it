---
title: Utilizzo degli Stati dell'entità-EF6
description: Utilizzo degli Stati dell'entità in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 88c1b67b3eda02e79f7d10d5e46fdd3566361634
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073769"
---
# <a name="working-with-entity-states"></a>Utilizzo degli Stati dell'entità
In questo argomento viene illustrato come aggiungere e alleghire entità a un contesto e come Entity Framework li elabora durante SaveChanges.
Entity Framework tiene traccia dello stato delle entità mentre sono connesse a un contesto, ma in scenari disconnessi o a più livelli è possibile consentire a EF di conoscere lo stato delle entità.
Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

## <a name="entity-states-and-savechanges"></a>Stati di entità e SaveChanges

Un'entità può essere in uno dei cinque stati in base a quanto definito dall'enumerazione EntityState. Questi stati sono:  

- Aggiunta: l'entità è stata rilevata dal contesto ma non esiste ancora nel database  
- Non modificato: l'entità viene rilevata dal contesto ed esiste nel database e i relativi valori delle proprietà non sono stati modificati rispetto ai valori nel database  
- Modificato: l'entità viene rilevata dal contesto ed esiste nel database e alcuni o tutti i valori delle relative proprietà sono stati modificati  
- Deleted: l'entità viene rilevata dal contesto ed è presente nel database, ma è stata contrassegnata per l'eliminazione dal database alla successiva chiamata a SaveChanges  
- Scollegato: l'entità non viene rilevata dal contesto  

SaveChanges esegue diverse operazioni per le entità in Stati diversi:  

- Le entità non modificate non vengono interessate da SaveChanges. Gli aggiornamenti non vengono inviati al database per le entità nello stato non modificato.  
- Le entità aggiunte vengono inserite nel database e quindi diventano invariate quando viene restituito SaveChanges.  
- Le entità modificate vengono aggiornate nel database e quindi diventano invariate quando viene restituito SaveChanges.  
- Le entità eliminate vengono eliminate dal database e quindi scollegate dal contesto.  

Negli esempi seguenti vengono illustrati i modi in cui è possibile modificare lo stato di un'entità o di un grafico di entità.  

## <a name="adding-a-new-entity-to-the-context"></a>Aggiunta di una nuova entità al contesto  

È possibile aggiungere una nuova entità al contesto chiamando il metodo Add su DbSet.
In questo modo l'entità viene inserita nello stato aggiunto, ovvero viene inserita nel database alla successiva chiamata a SaveChanges.
Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

Un altro modo per aggiungere una nuova entità al contesto consiste nel modificarne lo stato in added. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

Infine, è possibile aggiungere una nuova entità al contesto mediante l'associazione a un'altra entità già rilevata.
È possibile aggiungere la nuova entità alla proprietà di navigazione della raccolta di un'altra entità o impostando una proprietà di navigazione di riferimento di un'altra entità in modo che punti alla nuova entità. Ad esempio:  

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

Si noti che per tutti questi esempi, se l'entità aggiunta presenta riferimenti ad altre entità non ancora rilevate, anche queste nuove entità verranno aggiunte al contesto e verranno inserite nel database alla successiva chiamata di SaveChanges.  

## <a name="attaching-an-existing-entity-to-the-context"></a>Associazione di un'entità esistente al contesto  

Se si dispone di un'entità che è già presente nel database ma che attualmente non è stata rilevata dal contesto, è possibile indicare al contesto di tenere traccia dell'entità utilizzando il metodo di connessione su DbSet. L'entità si troverà nello stato non modificato nel contesto. Ad esempio:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

Si noti che non verrà apportata alcuna modifica al database se SaveChanges viene chiamato senza eseguire altre modifiche dell'entità associata. Questo è dovuto al fatto che l'entità si trova nello stato non modificato.  

Un altro modo per alleghire un'entità esistente al contesto consiste nel modificarne lo stato su Unchanged. Ad esempio:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Si noti che per entrambi questi esempi, se l'entità da collegare presenta riferimenti ad altre entità non ancora rilevate, anche queste nuove entità verranno associate al contesto nello stato Unchanged.  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>Associazione di un'entità esistente ma modificata al contesto  

Se si dispone di un'entità che è già presente nel database, ma in cui è possibile che siano state apportate modifiche, è possibile indicare al contesto di alleghire l'entità e impostarne lo stato su Modified.
Ad esempio:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

Quando si modifica lo stato in modificato, tutte le proprietà dell'entità verranno contrassegnate come modificate e tutti i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.  

Si noti che se l'entità da collegare presenta riferimenti ad altre entità non ancora rilevate, queste nuove entità verranno associate al contesto nello stato non modificato, ma non verranno apportate automaticamente.
Se sono presenti più entità che devono essere contrassegnate come modificate, è necessario impostare lo stato per ognuna di queste entità singolarmente.  

## <a name="changing-the-state-of-a-tracked-entity"></a>Modifica dello stato di un'entità rilevata  

È possibile modificare lo stato di un'entità già rilevata impostando la proprietà state sulla relativa voce. Ad esempio:  

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

Si noti che per modificare lo stato dell'entità è inoltre possibile utilizzare la chiamata a Add o Connetti per un'entità già rilevata. Ad esempio, se si chiama Connetti per un'entità attualmente nello stato aggiunto, il relativo stato verrà modificato in invariato.  

## <a name="insert-or-update-pattern"></a>Modello di inserimento o aggiornamento  

Un modello comune per alcune applicazioni consiste nell'aggiungere un'entità come nuova (con conseguente inserimento di un database) o nel collegare un'entità come esistente e contrassegnarla come modificata (causando un aggiornamento del database), a seconda del valore della chiave primaria.
Ad esempio, quando si usano chiavi primarie Integer generate dal database, è comune considerare un'entità con una chiave zero come nuova e un'entità con una chiave diversa da zero come esistente.
Questo modello può essere effettuato impostando lo stato dell'entità in base a un controllo del valore della chiave primaria. Ad esempio:  

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

Si noti che quando si modifica lo stato in modificato, tutte le proprietà dell'entità verranno contrassegnate come modificate e tutti i valori delle proprietà verranno inviati al database quando viene chiamato SaveChanges.  
