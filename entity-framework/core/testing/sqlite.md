---
title: Test con SQLite-EF Core
description: Uso di SQLite per testare un'applicazione Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: da2504cfe7997a10a5ee8c447b1c6ef00dd02369
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129057"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="75c70-103">Uso di SQLite per testare un'applicazione EF Core</span><span class="sxs-lookup"><span data-stu-id="75c70-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="75c70-104">L'uso di SQLite può essere un modo efficace per testare un'applicazione EF Core.</span><span class="sxs-lookup"><span data-stu-id="75c70-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="75c70-105">Tuttavia, i problemi possono verificarsi quando SQLite si comporta in modo diverso rispetto ad altri sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="75c70-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span>
> <span data-ttu-id="75c70-106">Vedere [test del codice che usa EF Core](xref:core/testing/index) per una descrizione dei problemi e dei compromessi.</span><span class="sxs-lookup"><span data-stu-id="75c70-106">See [Testing code that uses EF Core](xref:core/testing/index) for a discussion of the issues and trade-offs.</span></span>

<span data-ttu-id="75c70-107">Questo documento si basa sui concetti introdotti in [esempio che illustrano come testare le applicazioni che usano EF Core](xref:core/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="75c70-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample).</span></span>
<span data-ttu-id="75c70-108">Gli esempi di codice riportati di seguito provengono da questo esempio.</span><span class="sxs-lookup"><span data-stu-id="75c70-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="75c70-109">Uso di database in memoria SQLite</span><span class="sxs-lookup"><span data-stu-id="75c70-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="75c70-110">In genere, SQLite crea database come file semplici e accede al file in-process con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="75c70-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="75c70-111">Questa operazione è molto rapida, soprattutto quando si usa un' [unità SSD](https://en.wikipedia.org/wiki/Solid-state_drive)veloce.</span><span class="sxs-lookup"><span data-stu-id="75c70-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span>

<span data-ttu-id="75c70-112">SQLite può anche usare i database creati esclusivamente in memoria.</span><span class="sxs-lookup"><span data-stu-id="75c70-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="75c70-113">Questa operazione è facile da usare con EF Core purché si conosca la durata del database in memoria:</span><span class="sxs-lookup"><span data-stu-id="75c70-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>

* <span data-ttu-id="75c70-114">Il database viene creato al momento dell'apertura della connessione.</span><span class="sxs-lookup"><span data-stu-id="75c70-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="75c70-115">Il database viene eliminato quando viene chiusa la connessione a essa.</span><span class="sxs-lookup"><span data-stu-id="75c70-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="75c70-116">EF Core utilizzerà una connessione già aperta, quando ne viene specificata una e non tenterà mai di chiuderla.</span><span class="sxs-lookup"><span data-stu-id="75c70-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="75c70-117">Quindi, la chiave per l'uso di EF Core con un database SQLite in memoria consiste nell'aprire la connessione prima di passarla a EF.</span><span class="sxs-lookup"><span data-stu-id="75c70-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>

<span data-ttu-id="75c70-118">L' [esempio](xref:core/testing/testing-sample) ottiene questo risultato con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="75c70-118">The [sample](xref:core/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="75c70-119">Notare:</span><span class="sxs-lookup"><span data-stu-id="75c70-119">Notice:</span></span>

* <span data-ttu-id="75c70-120">Il `CreateInMemoryDatabase` metodo crea un database in memoria SQLite e ne apre la connessione.</span><span class="sxs-lookup"><span data-stu-id="75c70-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="75c70-121">L'oggetto creato `DbConnection` viene Estratto da `ContextOptions` e salvato.</span><span class="sxs-lookup"><span data-stu-id="75c70-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="75c70-122">La connessione viene eliminata quando il test viene eliminato in modo da evitare perdite di risorse.</span><span class="sxs-lookup"><span data-stu-id="75c70-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span>

> [!NOTE]
> <span data-ttu-id="75c70-123">Il [problema #16103](https://github.com/dotnet/efcore/issues/16103) è tenere traccia dei modi per semplificare la gestione della connessione.</span><span class="sxs-lookup"><span data-stu-id="75c70-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span>
