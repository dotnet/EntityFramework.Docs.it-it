---
title: Caricamento di dati correlati - EF Core
description: Diverse strategie per il caricamento di dati correlati con Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063673"
---
# <a name="loading-related-data"></a>Caricamento di dati correlati

Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate. Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.

* Il **[caricamento eager](xref:core/querying/related-data/eager)** indica che i dati correlati vengono caricati dal database come parte della query iniziale.
* Il **[caricamento esplicito](xref:core/querying/related-data/explicit)** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.
* Il **[caricamento lazy](xref:core/querying/related-data/lazy)** indica che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.

> [!TIP]
> È possibile visualizzare gli [esempi](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) in questa sezione su GitHub.
