---
title: Proprietà Shadow e Indexer-EF Core
description: Configurazione delle proprietà shadow e indexer in un modello di Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: d419de2da2a9fc29e675dde76e824217347d2e9c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003432"
---
# <a name="shadow-and-indexer-properties"></a>Proprietà Shadow e Indexer

Le proprietà shadow sono proprietà che non sono definite nella classe di entità .NET, ma sono definite per il tipo di entità nel modello di EF Core. Il valore e lo stato di queste proprietà vengono mantenuti esclusivamente nello strumento di rilevamento delle modifiche. Le proprietà shadow sono utili quando nel database sono presenti dati che non devono essere esposti sui tipi di entità mappati.

Le proprietà dell'indicizzatore sono proprietà del tipo di entità, supportate da un [indicizzatore](/dotnet/csharp/programming-guide/indexers/) nella classe di entità .NET. È possibile accedervi usando l'indicizzatore nelle istanze della classe .NET. Consente inoltre di aggiungere ulteriori proprietà al tipo di entità senza modificare la classe CLR.

## <a name="foreign-key-shadow-properties"></a>Proprietà Shadow chiave esterna

Le proprietà shadow vengono spesso utilizzate per le proprietà di chiave esterna, in cui la relazione tra due entità è rappresentata da un valore di chiave esterna nel database, ma la relazione viene gestita sui tipi di entità utilizzando le proprietà di navigazione tra i tipi di entità. Per convenzione, EF introdurrà una proprietà shadow quando viene individuata una relazione, ma non viene trovata alcuna proprietà di chiave esterna nella classe di entità dipendente.

La proprietà verrà denominata `<navigation property name><principal key property name>` (la navigazione sull'entità dipendente, che fa riferimento all'entità principale, viene utilizzata per la denominazione). Se il nome della proprietà chiave principale include il nome della proprietà di navigazione, il nome sarà semplicemente `<principal key property name>` . Se non è presente alcuna proprietà di navigazione nell'entità dipendente, il nome del tipo di entità viene usato al suo posto.

Il seguente listato di codice, ad esempio, comporterà l' `BlogId` introduzione di una proprietà shadow all' `Post` entità:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Configurazione delle proprietà shadow

Per configurare le proprietà shadow, è possibile usare l'API Fluent. Una volta chiamato l'overload di stringa di `Property` , è possibile concatenare qualsiasi chiamata di configurazione per altre proprietà. Nell'esempio seguente, poiché `Blog` non dispone di una proprietà CLR denominata `LastUpdated` , viene creata una proprietà shadow:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Se il nome fornito al `Property` metodo corrisponde al nome di una proprietà esistente (una proprietà shadow o un oggetto definito nella classe di entità), il codice configurerà la proprietà esistente anziché introdurre una nuova proprietà shadow.

## <a name="accessing-shadow-properties"></a>Accesso alle proprietà shadow

I valori delle proprietà shadow possono essere ottenuti e modificati tramite l' `ChangeTracker` API:

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

È possibile fare riferimento alle proprietà shadow nelle query LINQ tramite il `EF.Property` metodo statico:

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Non è possibile accedere alle proprietà shadow dopo una query senza rilevamento poiché le entità restituite non vengono rilevate dallo strumento di rilevamento delle modifiche.

## <a name="configuring-indexer-properties"></a>Configurazione delle proprietà dell'indicizzatore

È possibile usare l'API Fluent per configurare le proprietà dell'indicizzatore. Una volta chiamato il metodo `IndexerProperty` , è possibile concatenare qualsiasi chiamata di configurazione per le altre proprietà. Nell'esempio seguente viene `Blog` definito un indicizzatore che verrà usato per creare una proprietà dell'indicizzatore.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

Se il nome fornito al `IndexerProperty` metodo corrisponde al nome di una proprietà dell'indicizzatore esistente, il codice configurerà la proprietà esistente. Se il tipo di entità dispone di una proprietà, supportata da una proprietà nella classe di entità, viene generata un'eccezione perché le proprietà dell'indicizzatore devono essere accessibili solo tramite l'indicizzatore.

## <a name="property-bag-entity-types"></a>Tipi di entità contenitore delle proprietà

> [!NOTE]
> Il supporto per i tipi di entità contenitore delle proprietà è stato introdotto in EF Core 5,0.

I tipi di entità che contengono solo proprietà dell'indicizzatore sono noti come tipi di entità del contenitore delle proprietà. Questi tipi di entità non hanno proprietà shadow, ma EF creerà le proprietà dell'indicizzatore. Attualmente `Dictionary<string, object>` è supportato solo come tipo di entità contenitore delle proprietà. Deve essere configurata come tipo di entità condivisa con un nome univoco e la `DbSet` proprietà corrispondente deve essere implementata tramite una `Set` chiamata a.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
