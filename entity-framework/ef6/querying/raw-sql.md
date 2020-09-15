---
title: Query SQL non elaborate-EF6
description: Query SQL non elaborate in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: 30fab3029d71fb560493af279231e46828aa43d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073886"
---
# <a name="raw-sql-queries-ef6"></a>Query SQL non elaborate (EF6)

Entity Framework consente di eseguire una query utilizzando LINQ con le classi di entità. Tuttavia, in alcuni casi è possibile che si desideri eseguire query utilizzando SQL RAW direttamente sul database. Ciò include la chiamata alle stored procedure, che può essere utile per i modelli di Code First che attualmente non supportano il mapping alle stored procedure. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

## <a name="writing-sql-queries-for-entities"></a>Scrittura di query SQL per le entità  

Il metodo sqlQuery su DbSet consente di scrivere una query SQL non elaborata che restituirà istanze di entità. Gli oggetti restituiti verranno rilevati dal contesto esattamente come se fossero restituiti da una query LINQ. Esempio:  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

Si noti che, come per le query LINQ, la query non viene eseguita finché non vengono enumerati i risultati. nell'esempio precedente questa operazione viene eseguita con la chiamata a ToList.  

Prestare attenzione quando le query SQL non elaborate vengono scritte per due motivi. In primo luogo, la query deve essere scritta per assicurarsi che restituisca solo entità effettivamente del tipo richiesto. Ad esempio, quando si utilizzano funzionalità come l'ereditarietà, è facile scrivere una query che creerà entità di tipo CLR errato.  

In secondo luogo, alcuni tipi di query SQL non elaborata espongono potenziali rischi per la sicurezza, in particolare per gli attacchi SQL injection. Assicurarsi di usare i parametri nella query nel modo corretto per proteggersi da tali attacchi.  

### <a name="loading-entities-from-stored-procedures"></a>Caricamento di entità da stored procedure  

È possibile utilizzare DbSet. sqlQuery per caricare entità dai risultati di un stored procedure. Il codice seguente, ad esempio, chiama dbo. Procedura getblogs nel database:  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

È anche possibile passare parametri a un stored procedure usando la sintassi seguente:  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>Scrittura di query SQL per tipi non di entità  

Una query SQL che restituisce istanze di qualsiasi tipo, inclusi i tipi primitivi, può essere creata usando il metodo sqlQuery sulla classe database. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

I risultati restituiti da sqlQuery sul database non verranno mai rilevati dal contesto anche se gli oggetti sono istanze di un tipo di entità.  

## <a name="sending-raw-commands-to-the-database"></a>Invio di comandi non elaborati al database  

I comandi non di query possono essere inviati al database usando il metodo ExecuteSqlCommand nel database. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

Si noti che tutte le modifiche apportate ai dati nel database tramite ExecuteSqlCommand sono opache al contesto fino a quando le entità non vengono caricate o ricaricate dal database.  

### <a name="output-parameters"></a>Parametri di output  

Se vengono utilizzati parametri di output, i relativi valori non saranno disponibili fino a quando i risultati non sono stati letti completamente. A causa del comportamento sottostante di DbDataReader, vedere Recupero di [dati tramite un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) per altri dettagli.  
