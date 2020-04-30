---
title: Test con il database in memoria EF-EF Core
author: ajcvickers
description: Uso del database in memoria EF per testare un'applicazione EF Core
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538346"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="af53b-103">Test con il database in memoria EF</span><span class="sxs-lookup"><span data-stu-id="af53b-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="af53b-104">Il database in memoria EF si comporta spesso in modo diverso rispetto ai database relazionali.</span><span class="sxs-lookup"><span data-stu-id="af53b-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="af53b-105">Usare il database in memoria EF solo dopo aver compreso completamente i problemi e i compromessi necessari, come descritto in [test del codice che usa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="af53b-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="af53b-106">SQLite è un provider relazionale e può anche usare i database in memoria.</span><span class="sxs-lookup"><span data-stu-id="af53b-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="af53b-107">Si consiglia di usare questo metodo per eseguire il test per confrontare con maggiore precisione i comportamenti comuni del database relazionale.</span><span class="sxs-lookup"><span data-stu-id="af53b-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="af53b-108">Questo problema viene trattato nell' [uso di SQLite per testare un'applicazione EF Core](xref:core/miscellaneous/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="af53b-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="af53b-109">Le informazioni contenute in questa pagina sono ora presenti in altre posizioni:</span><span class="sxs-lookup"><span data-stu-id="af53b-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="af53b-110">Vedere [test del codice che usa EF Core](xref:core/miscellaneous/testing/index) per informazioni generali sul test con il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="af53b-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="af53b-111">Vedere l' [esempio che illustra come testare le applicazioni che usano EF Core](xref:core/miscellaneous/testing/testing-sample) per un esempio usando il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="af53b-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="af53b-112">Per informazioni generali sul database in memoria EF, vedere [il provider di database in memoria EF](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="af53b-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
