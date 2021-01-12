---
title: Test con il database EF In-Memory-EF Core
description: Uso del database in memoria EF per testare un'applicazione Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128810"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="62338-103">Test con il database EF In-Memory</span><span class="sxs-lookup"><span data-stu-id="62338-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="62338-104">Il database in memoria EF si comporta spesso in modo diverso rispetto ai database relazionali.</span><span class="sxs-lookup"><span data-stu-id="62338-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="62338-105">Usare il database in memoria EF solo dopo aver compreso completamente i problemi e i compromessi necessari, come descritto in [test del codice che usa EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="62338-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/testing/index).</span></span>

> [!TIP]
> <span data-ttu-id="62338-106">SQLite è un provider relazionale e può anche usare i database in memoria.</span><span class="sxs-lookup"><span data-stu-id="62338-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="62338-107">Si consiglia di usare questo metodo per eseguire il test per confrontare con maggiore precisione i comportamenti comuni del database relazionale.</span><span class="sxs-lookup"><span data-stu-id="62338-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="62338-108">Questo problema viene trattato nell' [uso di SQLite per testare un'applicazione EF Core](xref:core/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="62338-108">This is covered in [Using SQLite to test an EF Core application](xref:core/testing/sqlite).</span></span>

<span data-ttu-id="62338-109">Le informazioni contenute in questa pagina sono ora presenti in altre posizioni:</span><span class="sxs-lookup"><span data-stu-id="62338-109">The information on this page now lives in other locations:</span></span>

* <span data-ttu-id="62338-110">Vedere [test del codice che usa EF Core](xref:core/testing/index) per informazioni generali sul test con il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="62338-110">See [Testing code that uses EF Core](xref:core/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="62338-111">Vedere l' [esempio che illustra come testare le applicazioni che usano EF Core](xref:core/testing/testing-sample) per un esempio usando il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="62338-111">See [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="62338-112">Per informazioni generali sul database in memoria EF, vedere [il provider di database in memoria EF](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="62338-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
