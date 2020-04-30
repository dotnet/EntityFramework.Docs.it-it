---
title: Test con SQLite-EF Core
description: Uso di SQLite per testare un'applicazione EF Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538278"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="be285-103">Uso di SQLite per testare un'applicazione EF Core</span><span class="sxs-lookup"><span data-stu-id="be285-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="be285-104">L'uso di SQLite può essere un modo efficace per testare un'applicazione EF Core.</span><span class="sxs-lookup"><span data-stu-id="be285-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="be285-105">Tuttavia, i problemi possono verificarsi quando SQLite si comporta in modo diverso rispetto ad altri sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="be285-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="be285-106">Vedere [test del codice che usa EF Core](xref:core/miscellaneous/testing/index) per una descrizione dei problemi e dei compromessi.</span><span class="sxs-lookup"><span data-stu-id="be285-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="be285-107">Questo documento si basa sui concetti introdotti in [esempio che illustrano come testare le applicazioni che usano EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="be285-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="be285-108">Gli esempi di codice riportati di seguito provengono da questo esempio.</span><span class="sxs-lookup"><span data-stu-id="be285-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="be285-109">Uso di database in memoria SQLite</span><span class="sxs-lookup"><span data-stu-id="be285-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="be285-110">In genere, SQLite crea database come file semplici e accede al file in-process con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="be285-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="be285-111">Questa operazione è molto rapida, soprattutto quando si usa un' [unità SSD](https://en.wikipedia.org/wiki/Solid-state_drive)veloce.</span><span class="sxs-lookup"><span data-stu-id="be285-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="be285-112">SQLite può anche usare i database creati esclusivamente in memoria.</span><span class="sxs-lookup"><span data-stu-id="be285-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="be285-113">Questa operazione è facile da usare con EF Core purché si conosca la durata del database in memoria:</span><span class="sxs-lookup"><span data-stu-id="be285-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="be285-114">Il database viene creato al momento dell'apertura della connessione.</span><span class="sxs-lookup"><span data-stu-id="be285-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="be285-115">Il database viene eliminato quando viene chiusa la connessione a essa.</span><span class="sxs-lookup"><span data-stu-id="be285-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="be285-116">EF Core utilizzerà una connessione già aperta, quando ne viene specificata una e non tenterà mai di chiuderla.</span><span class="sxs-lookup"><span data-stu-id="be285-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="be285-117">Quindi, la chiave per l'uso di EF Core con un database SQLite in memoria consiste nell'aprire la connessione prima di passarla a EF.</span><span class="sxs-lookup"><span data-stu-id="be285-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="be285-118">L' [esempio](xref:core/miscellaneous/testing/testing-sample) ottiene questo risultato con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="be285-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="be285-119">Notare:</span><span class="sxs-lookup"><span data-stu-id="be285-119">Notice:</span></span>
* <span data-ttu-id="be285-120">Il `CreateInMemoryDatabase` metodo crea un database in memoria SQLite e ne apre la connessione.</span><span class="sxs-lookup"><span data-stu-id="be285-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="be285-121">L'oggetto `DbConnection` creato viene Estratto da `ContextOptions` e salvato.</span><span class="sxs-lookup"><span data-stu-id="be285-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="be285-122">La connessione viene eliminata quando il test viene eliminato in modo da evitare perdite di risorse.</span><span class="sxs-lookup"><span data-stu-id="be285-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="be285-123">Il [problema #16103](https://github.com/dotnet/efcore/issues/16103) è tenere traccia dei modi per semplificare la gestione della connessione.</span><span class="sxs-lookup"><span data-stu-id="be285-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
