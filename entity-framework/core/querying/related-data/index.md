---
title: Caricamento di dati correlati - EF Core
description: Diverse strategie per il caricamento di dati correlati con Entity Framework Core
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078950"
---
# <a name="loading-related-data"></a><span data-ttu-id="85714-103">Caricamento di dati correlati</span><span class="sxs-lookup"><span data-stu-id="85714-103">Loading Related Data</span></span>

<span data-ttu-id="85714-104">Entity Framework Core consente di usare le proprietà di navigazione nel modello per caricare entità correlate.</span><span class="sxs-lookup"><span data-stu-id="85714-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="85714-105">Esistono tre modelli di O/RM (Object-Relational Mapping) comuni usati per caricare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="85714-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="85714-106">Il **[caricamento eager](xref:core/querying/related-data/eager)** indica che i dati correlati vengono caricati dal database come parte della query iniziale.</span><span class="sxs-lookup"><span data-stu-id="85714-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="85714-107">Il **[caricamento esplicito](xref:core/querying/related-data/explicit)** significa che i dati correlati vengono caricati in modo esplicito dal database in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="85714-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="85714-108">Il **[caricamento lazy](xref:core/querying/related-data/lazy)** indica che i dati correlati vengono caricati in modo trasparente dal database quando si accede alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="85714-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="85714-109">È possibile visualizzare gli [esempi](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) in questa sezione su GitHub.</span><span class="sxs-lookup"><span data-stu-id="85714-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) under this section on GitHub.</span></span>
