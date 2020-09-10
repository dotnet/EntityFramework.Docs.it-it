---
title: Nuove funzionalità di EF Core 2.2 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 2,2
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 5d248c4dcf760ee7ae86417f5d7a000848a4f97e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618728"
---
# <a name="new-features-in-ef-core-22"></a>Nuove funzionalità di EF Core 2.2

## <a name="spatial-data-support"></a>Supporto di dati spaziali

Si possono usare dati spaziali per rappresentare la posizione fisica e la forma degli oggetti.
Molti database possono archiviare, indicizzare ed eseguire query sui dati spaziali in modo nativo.
Sono esempi di scenari comuni l'esecuzione di query per recuperare gli oggetti entro una distanza specificata e il test della presenza di una posizione specifica in un poligono.
EF Core 2.2 supporta ora l'utilizzo dei dati spaziali da vari database usando i tipi della libreria [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS).

Il supporto dei dati spaziali viene implementato come una serie di pacchetti di estensione specifici del provider.
Ognuno di questi pacchetti fornisce i mapping per tipi e i metodi NTS, nonché i tipi e le funzioni spaziali corrispondenti nel database.
Queste estensioni del provider sono ora disponibili per [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) e [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (dal [progetto Npgsql](https://www.npgsql.org/)).
I tipi spaziali possono essere usati direttamente con il [provider in memoria EF Core](xref:core/providers/in-memory/index) senza estensioni aggiuntive.

Dopo aver installato l'estensione del provider, è possibile aggiungere proprietà dei tipi supportati alle entità. Ad esempio:

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
```

È quindi possibile rendere persistenti le entità con dati spaziali:

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```

Ed è possibile eseguire query di database basate su operazioni e dati spaziali:

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Per altre informazioni su questa funzionalità, vedere la [documentazione sui tipi spaziali](xref:core/modeling/spatial).

## <a name="collections-of-owned-entities"></a>Raccolte di entità di proprietà

In EF Core 2.0 è stata aggiunta la possibilità di modellare la proprietà nelle associazioni uno-a-uno.
EF Core 2.2 estende la possibilità di esprimere la proprietà alle associazioni uno-a-molti.
La proprietà consente di vincolare l'uso delle entità.

Ad esempio, le entità di proprietà:

- Possono comparire solo nelle proprietà di navigazione di altri tipi di entità.
- Vengono caricate automaticamente e possono essere gestite solo da un DbContext insieme al proprietario.

Nei database relazionali, le raccolte di proprietà vengono mappate a tabelle separate dal proprietario, proprio come le normali associazioni uno-a-molti.
Tuttavia, nei database orientati ai documenti è previsto l'annidamento delle entità di proprietà (in raccolte di proprietà o riferimenti) all'interno dello stesso documento del proprietario.

È possibile usare la funzionalità chiamando la nuova API OwnsMany():

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

Per altre informazioni, vedere la [documentazione aggiornata sulle entità di proprietà](xref:core/modeling/owned-entities#collections-of-owned-types).

## <a name="query-tags"></a>Tag delle query

Questa funzionalità semplifica la correlazione di query LINQ nel codice con query SQL generate acquisite nei log.

Per sfruttare i vantaggi dei tag delle query, è possibile annotare una query LINQ con il nuovo metodo TagWith().
Se si usa la query spaziale da un esempio precedente:

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Questa query LINQ produrrà l'output SQL seguente:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Per altre informazioni, vedere la [documentazione sui tag delle query](xref:core/querying/tags).
