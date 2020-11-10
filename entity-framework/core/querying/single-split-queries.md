---
title: Query single vs. Split-EF Core
description: Conversione di query in query singole e divise in SQL con Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1c99d931c01b99de199710ffe661e1aac7a37263
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431627"
---
# <a name="single-vs-split-queries"></a><span data-ttu-id="ea5e7-103">Query singole e Split</span><span class="sxs-lookup"><span data-stu-id="ea5e7-103">Single vs. split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="ea5e7-104">Query singole</span><span class="sxs-lookup"><span data-stu-id="ea5e7-104">Single queries</span></span>

<span data-ttu-id="ea5e7-105">Nei database relazionali tutte le entità correlate vengono caricate introducendo JOIN in una singola query.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="ea5e7-106">Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="ea5e7-107">Questa duplicazione porta al cosiddetto problema "esplosione cartesiana".</span><span class="sxs-lookup"><span data-stu-id="ea5e7-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="ea5e7-108">Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="ea5e7-109">Suddividere query</span><span class="sxs-lookup"><span data-stu-id="ea5e7-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="ea5e7-110">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-110">This feature is introduced in EF Core 5.0.</span></span> <span data-ttu-id="ea5e7-111">Funziona solo quando si usa `Include` .</span><span class="sxs-lookup"><span data-stu-id="ea5e7-111">It only works when using `Include`.</span></span> <span data-ttu-id="ea5e7-112">[Questo problema](https://github.com/dotnet/efcore/issues/21234) tiene traccia del supporto per la query Split durante il caricamento dei dati correlati nella proiezione senza `Include` .</span><span class="sxs-lookup"><span data-stu-id="ea5e7-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="ea5e7-113">EF consente di specificare che una query LINQ specificata deve essere *suddivisa* in più query SQL.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="ea5e7-114">Anziché JOIN, le query suddivise generano una query SQL aggiuntiva per ogni navigazione della raccolta inclusa:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="ea5e7-115">Verrà generato il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="ea5e7-116">Le entità correlate uno-a-uno vengono sempre caricate tramite JOIN nella stessa query, in quanto non ha alcun effetto sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="ea5e7-117">Abilitazione di query Split a livello globale</span><span class="sxs-lookup"><span data-stu-id="ea5e7-117">Enabling split queries globally</span></span>

<span data-ttu-id="ea5e7-118">È anche possibile configurare le query Split come predefinite per il contesto dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="ea5e7-119">Quando le query suddivise sono configurate come predefinite, è comunque possibile configurare query specifiche da eseguire come query singole:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="ea5e7-120">Per impostazione predefinita, EF Core usa la modalità query singola in assenza di qualsiasi configurazione.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="ea5e7-121">Poiché può causare problemi di prestazioni, EF Core genera un avviso ogni volta che vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="ea5e7-122">EF Core rileva che la query carica più raccolte.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="ea5e7-123">L'utente non ha configurato la modalità di suddivisione delle query a livello globale.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="ea5e7-124">L'utente non ha usato `AsSingleQuery` / `AsSplitQuery` l'operatore nella query.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="ea5e7-125">Per disabilitare l'avviso, configurare la modalità di suddivisione delle query a livello globale o a livello di query su un valore appropriato.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="ea5e7-126">Caratteristiche delle query suddivise</span><span class="sxs-lookup"><span data-stu-id="ea5e7-126">Characteristics of split queries</span></span>

<span data-ttu-id="ea5e7-127">Mentre Split query evita i problemi di prestazioni associati a JOIN e l'esplosione cartesiana, presenta anche alcuni svantaggi:</span><span class="sxs-lookup"><span data-stu-id="ea5e7-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="ea5e7-128">Anche se la maggior parte dei database garantisce la coerenza dei dati per le singole query, non esistono garanzie di questo tipo per più query.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="ea5e7-129">Se il database viene aggiornato simultaneamente durante l'esecuzione delle query, i dati risultanti potrebbero non essere coerenti.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="ea5e7-130">È possibile mitigarlo eseguendo il wrapping delle query in una transazione serializzabile o snapshot, anche se in questo modo è possibile creare problemi di prestazioni propri.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="ea5e7-131">Per ulteriori informazioni, vedere la documentazione del database.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="ea5e7-132">Ogni query implica attualmente un round trip di rete aggiuntivo al database.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="ea5e7-133">Il round trip della rete può compromettere le prestazioni, soprattutto se la latenza del database è elevata, ad esempio servizi cloud.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-133">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="ea5e7-134">Sebbene alcuni database consentano di usare contemporaneamente i risultati di più query (SQL Server con MARS, SQLite), la maggior parte consente di rendere attiva una sola query in un determinato punto.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="ea5e7-135">Tutti i risultati delle query precedenti devono quindi essere memorizzati nel buffer nella memoria dell'applicazione prima di eseguire query successive, il che comporta un aumento dei requisiti di memoria.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="ea5e7-136">Sfortunatamente, non esiste una strategia per il caricamento di entità correlate che soddisfino tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="ea5e7-137">Valutare con attenzione i vantaggi e gli svantaggi delle query singole e divise per selezionare quello che soddisfa le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="ea5e7-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
