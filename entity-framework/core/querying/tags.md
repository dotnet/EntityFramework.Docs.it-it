---
title: Tag delle query - EF Core
description: Uso dei tag di query per identificare query specifiche nei messaggi di log emessi da Entity Framework Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: bf80057d29160bd5ef6ae26ee6d447fc3c3f0fec
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617490"
---
# <a name="query-tags"></a>Tag delle query

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 2.2.

Questa funzionalità consente di correlare le query LINQ nel codice con query SQL generate acquisite nei log.
È possibile annotare una query LINQ usando il nuovo metodo `TagWith()`:

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Questa query LINQ viene convertita nell'istruzione SQL seguente:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

È possibile chiamare `TagWith()` più volte sulla stessa query.
I tag delle query sono cumulativi.
Considerati i metodi seguenti, ad esempio:

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

La query seguente:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

Viene convertita in:

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

È anche possibile usare stringhe con più righe come tag di query.
Ad esempio:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

Produce il codice SQL seguente:

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Limitazioni note

**I tag delle query non sono parametrizzabili:** EF Core considera sempre i tag delle query nella query LINQ come valori letterali stringa inclusi nel codice SQL generato.
Non sono consentite query compilate che accettano i tag di query come parametri.
