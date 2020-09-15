---
title: Query e ricerca di entità - EF6
description: Esecuzione di query e ricerca di entità in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 65bb3db2-2226-44af-8864-caa575cf1b46
uid: ef6/querying/index
ms.openlocfilehash: 4bfdc59527ab75e7a2e08dc743a1974246643aa3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620335"
---
# <a name="querying-and-finding-entities"></a>Query e ricerca di entità
Questo argomento illustra i vari modi che è possibile usare per la ricerca di dati tramite Entity Framework, inclusi LINQ e il metodo Find. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

## <a name="finding-entities-using-a-query"></a>Ricerca di entità tramite query  

DbSet e IDbSet implementano IQueryable e possono quindi essere usati come punto di partenza per scrivere una query LINQ sul database. In questo articolo non si affronterà una discussione approfondita su LINQ, ma vengono comunque riportati due semplici esempi:  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

Si noti che DbSet e IDbSet creano sempre query sul database e determineranno sempre un round trip al database anche se le entità restituite esistono già nel contesto. Viene eseguita una query sul database quando:  

- È stata enumerata da un'istruzione **foreach** (C#) o **For Each** (Visual Basic).  
- È stata enumerata da un'operazione di raccolta, ad esempio [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) o [ToList](https://msdn.microsoft.com/library/bb342261).  
- Operatori LINQ quali [First](https://msdn.microsoft.com/library/bb291976) o [Any](https://msdn.microsoft.com/library/bb337697) vengono specificati nella parte più esterna della query.  
- Vengono chiamati i metodi seguenti: il metodo di estensione [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) su DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) e Database.ExecuteSqlCommand.  

Quando vengono restituiti i risultati dal database, gli oggetti che non esistono nel contesto vengono collegati al contesto. Se un oggetto è già presente nel contesto, viene restituito l'oggetto esistente (i valori corrente e originale delle relative proprietà nella voce **non** vengono sovrascritti con i valore del database).  

Quando si esegue una query, le entità aggiunte al contesto ma non ancora salvate nel database non vengono restituite nel set di risultati. Per ottenere i dati presenti nel contesto, vedere [Local Data](xref:ef6/querying/local-data) (Dati locali).  

Se una query non restituisce righe dal database, il risultato sarà una raccolta vuota, anziché **Null**.  

## <a name="finding-entities-using-primary-keys"></a>Ricerca di entità tramite chiavi primarie  

Il metodo Find su DbSet usa il valore della chiave primaria per tentare di individuare un'entità rilevata dal contesto. Se l'entità non viene individuata nel contesto, viene inviata una query al database per individuarla al suo interno. Se l'entità non è presente né nel contesto, né nel database, viene restituito Null.  

Il metodo Find si differenzia dall'uso di una query in due modi:  

- Un round trip al database viene eseguito solo se l'entità con la chiave specificata non è stata individuata nel contesto.  
- Find restituisce le entità con lo stato Added, ovvero le entità che sono state aggiunte al contesto, ma che non sono ancora state salvate nel database.  
### <a name="finding-an-entity-by-primary-key"></a>Ricerca di un'entità in base alla chiave primaria  

Il codice seguente illustra alcuni usi del metodo Find:  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>Ricerca di un'entità in base alla chiave primaria composta  

In Entity Framework le entità possono avere chiavi composte, ovvero chiavi costituite da più di una proprietà. È possibile, ad esempio, avere un'entità BlogSettings che rappresenta le impostazioni di un utente per un particolare blog. Poiché un utente avrà sempre una sola entità BlogSettings per ogni blog, la chiave primaria di BlogSettings può essere una combinazione di ID blog e nome utente. Il codice seguente prova a cercare l'entità BlogSettings con ID blog = 3 e nome utente = "johndoe1987":  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

Si noti che quando si usano chiavi composte è necessario usare ColumnAttribute o l'API Fluent per specificare l'ordinamento delle proprietà della chiave composta. La chiamata al metodo Find deve usare quest'ordine quando vengono specificati i valori che compongono la chiave.  
