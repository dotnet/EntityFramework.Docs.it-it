---
title: Caricamento esplicito dei dati correlati-EF Core
description: Caricamento esplicito di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: def1c8c2736c64bc6f39af0d344fd1330f8a06f5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210454"
---
# <a name="explicit-loading-of-related-data"></a>Caricamento esplicito di dati correlati

## <a name="explicit-loading"></a>Caricamento esplicito

È possibile caricare in modo esplicito una proprietà di navigazione tramite l'API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

È anche possibile caricare in modo esplicito una proprietà di navigazione eseguendo una query separata che restituisce le entità correlate. Se il rilevamento delle modifiche è abilitato, quando la query materializza un'entità, EF Core imposterà automaticamente le proprietà di navigazione dell'entità appena caricata in modo da fare riferimento alle entità già caricate e impostare le proprietà di navigazione delle entità già caricate in modo che facciano riferimento all'entità appena caricata.

## <a name="querying-related-entities"></a>Esecuzione di query su entità correlate

È anche possibile ottenere una query LINQ che rappresenta il contenuto di una proprietà di navigazione.

Consente di applicare operatori aggiuntivi alla query. Ad esempio applicando un operatore di aggregazione sulle entità correlate senza caricarle in memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

È anche possibile filtrare le entità correlate che vengono caricate in memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
