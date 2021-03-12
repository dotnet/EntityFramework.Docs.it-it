---
title: Caricamento di dati correlati - EF Core
description: Diverse strategie per il caricamento di dati correlati con Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: 8d7fa1ac5673fe4289b18c5b8e12563683463fe8
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023718"
---
# <a name="loading-related-data"></a>Caricamento di dati correlati

Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate. Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.

* Il **[caricamento eager](xref:core/querying/related-data/eager)** indica che i dati correlati vengono caricati dal database come parte della query iniziale.
* Il **[caricamento esplicito](xref:core/querying/related-data/explicit)** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.
* Il **[caricamento lazy](xref:core/querying/related-data/lazy)** indica che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.

> [!TIP]
> È possibile visualizzare gli [esempi](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/RelatedData) in questa sezione su GitHub.
