---
title: Programmazione asincrona-EF Core
description: Esecuzione di query e salvataggio asincrono dei dati con Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: 52b3ac20a50babbed6937ebe3365ac1947dcaef1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128576"
---
# <a name="asynchronous-programming"></a><span data-ttu-id="d68c5-103">Programmazione asincrona</span><span class="sxs-lookup"><span data-stu-id="d68c5-103">Asynchronous Programming</span></span>

<span data-ttu-id="d68c5-104">Le operazioni asincrone evitano il blocco di un thread mentre la query viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="d68c5-104">Asynchronous operations avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="d68c5-105">Le operazioni asincrone sono importanti per mantenere un'interfaccia utente reattiva nelle applicazioni rich client e possono anche aumentare la velocità effettiva nelle applicazioni Web in cui liberano il thread per soddisfare altre richieste nelle applicazioni Web.</span><span class="sxs-lookup"><span data-stu-id="d68c5-105">Async operations are important for keeping a responsive UI in rich client applications, and can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span>

<span data-ttu-id="d68c5-106">In base a .NET standard, EF Core fornisce controparti asincrone a tutti i metodi sincroni che eseguono operazioni di I/O.</span><span class="sxs-lookup"><span data-stu-id="d68c5-106">Following the .NET standard, EF Core provides asynchronous counterparts to all synchronous methods which perform I/O.</span></span> <span data-ttu-id="d68c5-107">Questi hanno gli stessi effetti dei metodi di sincronizzazione e possono essere usati con le `async` `await` parole chiave C# e.</span><span class="sxs-lookup"><span data-stu-id="d68c5-107">These have the same effects as the sync methods, and can be used with the C# `async` and `await` keywords.</span></span> <span data-ttu-id="d68c5-108">Ad esempio, invece di usare DbContext. SaveChanges, che bloccherà un thread durante l'esecuzione dell'I/O del database, è possibile usare DbContext. SaveChangesAsync:</span><span class="sxs-lookup"><span data-stu-id="d68c5-108">For example, instead of using DbContext.SaveChanges, which will block a thread while database I/O is performed, DbContext.SaveChangesAsync can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

<span data-ttu-id="d68c5-109">Per ulteriori informazioni, vedere [la documentazione generale per la programmazione asincrona in C#](/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="d68c5-109">For more information, see [the general C# asynchronous programming docs](/dotnet/csharp/async).</span></span>

> [!WARNING]
> <span data-ttu-id="d68c5-110">EF Core non supporta l'esecuzione di più operazioni parallele nella stessa istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="d68c5-110">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="d68c5-111">È sempre necessario attendere il completamento di un'operazione prima di iniziare l'operazione successiva.</span><span class="sxs-lookup"><span data-stu-id="d68c5-111">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="d68c5-112">Questa operazione viene in genere eseguita usando la `await` parola chiave in ogni operazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="d68c5-112">This is typically done by using the `await` keyword on each async operation.</span></span>

> [!WARNING]
> <span data-ttu-id="d68c5-113">Sfortunatamente, l'implementazione asincrona di [Microsoft. Data. SqlClient](https://github.com/dotnet/SqlClient) presenta alcuni problemi noti (ad esempio [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601)e altri).</span><span class="sxs-lookup"><span data-stu-id="d68c5-113">The async implementation of [Microsoft.Data.SqlClient](https://github.com/dotnet/SqlClient) unfortunately has some known issues (e.g. [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601), and others).</span></span>

> [!NOTE]
> <span data-ttu-id="d68c5-114">EF Core passa i token di annullamento al provider di database sottostante in uso (ad esempio, Microsoft. Data. SqlClient).</span><span class="sxs-lookup"><span data-stu-id="d68c5-114">EF Core passes cancellation tokens down to the underlying database provider in use (e.g. Microsoft.Data.SqlClient).</span></span> <span data-ttu-id="d68c5-115">Questi token possono o meno essere rispettati. consultare la documentazione del provider di database.</span><span class="sxs-lookup"><span data-stu-id="d68c5-115">These tokens may or may not be honored - consult your database provider's documentation.</span></span>

## <a name="async-linq-operators"></a><span data-ttu-id="d68c5-116">Operatori LINQ asincroni</span><span class="sxs-lookup"><span data-stu-id="d68c5-116">Async LINQ operators</span></span>

<span data-ttu-id="d68c5-117">Per supportare l'esecuzione asincrona di query LINQ, EF Core fornisce un set di metodi di estensione asincroni che eseguono la query e restituiscono risultati.</span><span class="sxs-lookup"><span data-stu-id="d68c5-117">In order to support executing LINQ queries asynchronously, EF Core provides a set of async extension methods which execute the query and return results.</span></span> <span data-ttu-id="d68c5-118">Queste controparti per gli operatori LINQ standard e sincroni includono ToListAsync, SingleAsync, AsAsyncEnumerable e così via:</span><span class="sxs-lookup"><span data-stu-id="d68c5-118">These counterparts to the standard, synchronous LINQ operators include ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

<span data-ttu-id="d68c5-119">Si noti che non sono presenti versioni asincrone di alcuni operatori LINQ, ad esempio WHERE o OrderBy, perché questi compilano solo l'albero delle espressioni LINQ e non causano l'esecuzione della query nel database.</span><span class="sxs-lookup"><span data-stu-id="d68c5-119">Note that there are no async versions of some LINQ operators such as Where or OrderBy, because these only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span> <span data-ttu-id="d68c5-120">Solo gli operatori che provocano l'esecuzione di query hanno controparti asincrone.</span><span class="sxs-lookup"><span data-stu-id="d68c5-120">Only operators which cause query execution have async counterparts.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d68c5-121">I metodi di estensione asincroni di EF Core sono definiti nello spazio dei nomi `Microsoft.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="d68c5-121">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="d68c5-122">Questo spazio dei nomi deve essere importato affinché i metodi siano disponibili.</span><span class="sxs-lookup"><span data-stu-id="d68c5-122">This namespace must be imported for the methods to be available.</span></span>

## <a name="client-side-async-linq-operators"></a><span data-ttu-id="d68c5-123">Operatori LINQ asincroni sul lato client</span><span class="sxs-lookup"><span data-stu-id="d68c5-123">Client-side async LINQ operators</span></span>

<span data-ttu-id="d68c5-124">Gli operatori LINQ asincroni descritti in precedenza possono essere usati solo in query EF. non è possibile usarli con la query LINQ to Objects sul lato client.</span><span class="sxs-lookup"><span data-stu-id="d68c5-124">The async LINQ operators discussed above can only be used on EF queries - you cannot use them with client-side LINQ to Objects query.</span></span> <span data-ttu-id="d68c5-125">Per eseguire operazioni LINQ asincrone sul lato client al di fuori di EF, usare il [pacchetto System. Interactive. Async.](https://www.nuget.org/packages/System.Interactive.Async) Questo pacchetto può essere particolarmente utile per l'esecuzione di operazioni sul client che non possono essere convertite per la valutazione nel server.</span><span class="sxs-lookup"><span data-stu-id="d68c5-125">To perform client-side async LINQ operations outside of EF, use the [System.Interactive.Async package](https://www.nuget.org/packages/System.Interactive.Async); this package can be especially useful for performing operations on the client that cannot be translated for evaluation at the server.</span></span>

<span data-ttu-id="d68c5-126">Sfortunatamente, il riferimento a System. Interactive. Async causa errori di compilazione ambigui per gli operatori LINQ applicati a DbSet di EF; Ciò rende difficile l'uso di EF e System. Interactive. Async nello stesso progetto.</span><span class="sxs-lookup"><span data-stu-id="d68c5-126">Unfortunately, referencing System.Interactive.Async causes ambiguous invocation compilation errors on LINQ operators applied to EF's DbSets; this makes it hard to use both EF and System.Interactive.Async in the same project.</span></span> <span data-ttu-id="d68c5-127">Per risolvere questo problema, aggiungere AsQueryable alla DbSet:</span><span class="sxs-lookup"><span data-stu-id="d68c5-127">To work around this issue, add AsQueryable to your DbSet:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
