---
title: EF6 ed EF Core - Uso nella stessa applicazione
description: Indicazioni sull'uso di Entity Framework Core e Entity Framework 6 nella stessa applicazione
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 68549009868a63f50d34ea8829de55574c891d19
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064198"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="c7bf9-103">Using di EF Core e di EF6 nella stessa applicazione</span><span class="sxs-lookup"><span data-stu-id="c7bf9-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="c7bf9-104">È possibile usare EF Core e EF6 nella stessa applicazione o nella stessa libreria installando entrambi i pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7bf9-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="c7bf9-105">Poiché alcuni tipi hanno lo stesso nome in EF Core e in EF6 e si distinguono solo per lo spazio dei nomi, l'uso di EF Core ed EF6 nello stesso file di codice può risultare complicato.</span><span class="sxs-lookup"><span data-stu-id="c7bf9-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="c7bf9-106">L'ambiguità può essere facilmente eliminata usando direttive alias dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c7bf9-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="c7bf9-107">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c7bf9-107">For example:</span></span>

```csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="c7bf9-108">Se si deve convertire un'applicazione esistente con più modelli EF, è possibile scegliere di convertirne solo alcuni in EF Core e di continuare a usare EF6 per gli altri.</span><span class="sxs-lookup"><span data-stu-id="c7bf9-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
