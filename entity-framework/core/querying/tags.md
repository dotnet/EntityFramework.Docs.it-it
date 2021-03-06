---
title: Tag delle query - EF Core
description: Uso dei tag di query per identificare query specifiche nei messaggi di log emessi da Entity Framework Core
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: bd71b8ce1cdb33aae417a90e84861de92224daa3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023770"
---
# <a name="query-tags"></a>Tag delle query

I tag di query consentono di correlare le query LINQ nel codice con query SQL generate acquisite nei log.
È possibile annotare una query LINQ usando il nuovo metodo `TagWith()`:

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Tags) di questo articolo in GitHub.

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Questa query LINQ viene convertita nell'istruzione SQL seguente:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

È possibile chiamare `TagWith()` più volte sulla stessa query.
I tag delle query sono cumulativi.
Considerati i metodi seguenti, ad esempio:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

La query seguente:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

Viene convertita in:

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

È anche possibile usare stringhe con più righe come tag di query.
Ad esempio:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

Produce il codice SQL seguente:

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Limitazioni note

**I tag delle query non sono parametrizzabili:** EF Core considera sempre i tag delle query nella query LINQ come valori letterali stringa inclusi nel codice SQL generato.
Non sono consentite query compilate che accettano i tag di query come parametri.
