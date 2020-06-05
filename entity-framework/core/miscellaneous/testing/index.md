---
title: Test del codice che usa EF Core - EF Core
description: Approcci diversi al test delle applicazioni che usano EF Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: a5ff85a60af1f56a0924d6fa0646195146a6827e
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418910"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="215c0-103">Test del codice che usa EF Core</span><span class="sxs-lookup"><span data-stu-id="215c0-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="215c0-104">Il test del codice che accede a un database richiede:</span><span class="sxs-lookup"><span data-stu-id="215c0-104">Testing code that accesses a database requires either:</span></span>
* <span data-ttu-id="215c0-105">Esecuzione di query e aggiornamenti sullo stesso sistema di database usato nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="215c0-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="215c0-106">Esecuzione di query e aggiornamenti su altri sistemi di database più facili da gestire.</span><span class="sxs-lookup"><span data-stu-id="215c0-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="215c0-107">Usare duplicati di test o un altro meccanismo per evitare del tutto di usare un database.</span><span class="sxs-lookup"><span data-stu-id="215c0-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="215c0-108">In questo documento vengono illustrati i pro e contro di ognuna di queste scelte e viene illustrato il modo in cui è possibile usare EF Core con ogni approccio.</span><span class="sxs-lookup"><span data-stu-id="215c0-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

> [!TIP]
> <span data-ttu-id="215c0-109">Per esaminare un esempio di codice che illustra i concetti introdotti in questo articolo, vedere [Esempio di test di EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="215c0-109">See [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span> 

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="215c0-110">Non tutti i provider di database sono uguali</span><span class="sxs-lookup"><span data-stu-id="215c0-110">All database providers are not equal</span></span>

<span data-ttu-id="215c0-111">È molto importante comprendere che EF Core non è progettato per astrarre ogni aspetto del sistema di database sottostante.</span><span class="sxs-lookup"><span data-stu-id="215c0-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="215c0-112">EF Core è invece un set comune di modelli e concetti che può essere usato con qualsiasi sistema di database.</span><span class="sxs-lookup"><span data-stu-id="215c0-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="215c0-113">I provider di database EF Core sovrappongono quindi il comportamento e le funzionalità specifici del database su questo framework comune.</span><span class="sxs-lookup"><span data-stu-id="215c0-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="215c0-114">Questo consente a ogni sistema di database di eseguire le funzionalità peculiari, mantenendo comunque i punti in comune, laddove appropriato, con altri sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="215c0-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span> 

<span data-ttu-id="215c0-115">Fondamentalmente, questo significa che cambiare provider di database cambierà il comportamento di EF Core e non ci può aspettare che l'applicazione funzioni correttamente a meno che non si tenga conto in modo esplicito delle eventuali differenze a livello di comportamento.</span><span class="sxs-lookup"><span data-stu-id="215c0-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="215c0-116">Detto questo, in molti casi questo cambiamento non creerà problemi perché esiste un elevato livello di elementi in comune tra i database relazionali.</span><span class="sxs-lookup"><span data-stu-id="215c0-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="215c0-117">Questo aspetto è sia positivo che negativo.</span><span class="sxs-lookup"><span data-stu-id="215c0-117">This is good and bad.</span></span>
<span data-ttu-id="215c0-118">Positivo perché spostarsi tra sistemi di database diversi può essere relativamente semplice.</span><span class="sxs-lookup"><span data-stu-id="215c0-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="215c0-119">Negativo perché può dare un falso senso di sicurezza se l'applicazione non è stata completamente testata per il nuovo sistema di database.</span><span class="sxs-lookup"><span data-stu-id="215c0-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="215c0-120">Approccio 1: Sistema di database di produzione</span><span class="sxs-lookup"><span data-stu-id="215c0-120">Approach 1: Production database system</span></span>

<span data-ttu-id="215c0-121">Come descritto nella sezione precedente, l'unico modo per assicurarsi di testare l'esecuzione in produzione consiste nell'usare lo stesso sistema di database.</span><span class="sxs-lookup"><span data-stu-id="215c0-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="215c0-122">Ad esempio, se l'applicazione distribuita usa SQL Azure, anche i test devono essere eseguiti su SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="215c0-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="215c0-123">Il processo sarebbe tuttavia lento e oneroso, se ogni sviluppatore eseguisse i test su SQL Azure lavorando attivamente al codice in contemporanea.</span><span class="sxs-lookup"><span data-stu-id="215c0-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="215c0-124">Questo è in effetti il compromesso principale da raggiungere per questi approcci: quando è opportuno deviare dal sistema di database di produzione per migliorare l'efficienza dei test?</span><span class="sxs-lookup"><span data-stu-id="215c0-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="215c0-125">Fortunatamente, in questo caso la risposta è piuttosto semplice: usare SQL Server in locale per i test degli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="215c0-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="215c0-126">SQL Azure e SQL Server sono molto simili, quindi il test su SQL Server è in genere un compromesso ragionevole.</span><span class="sxs-lookup"><span data-stu-id="215c0-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="215c0-127">Detto questo, è comunque saggio eseguire i test su SQL Azure stesso prima di passare all'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="215c0-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>
 
### <a name="localdb"></a><span data-ttu-id="215c0-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="215c0-128">LocalDB</span></span> 

<span data-ttu-id="215c0-129">Tutti i principali sistemi di database hanno una qualche forma di "edizione per sviluppatori" per i test locali.</span><span class="sxs-lookup"><span data-stu-id="215c0-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="215c0-130">SQL Server include anche una funzionalità denominata [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span><span class="sxs-lookup"><span data-stu-id="215c0-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span></span>
<span data-ttu-id="215c0-131">Il vantaggio principale di LocalDB è che l'istanza del database viene attivata su richiesta.</span><span class="sxs-lookup"><span data-stu-id="215c0-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="215c0-132">In questo modo si evita l'esecuzione di un servizio di database nel computer anche quando non vengono eseguiti test.</span><span class="sxs-lookup"><span data-stu-id="215c0-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="215c0-133">LocalDB non è privo di problemi:</span><span class="sxs-lookup"><span data-stu-id="215c0-133">LocalDB is not without its issues:</span></span>
* <span data-ttu-id="215c0-134">Non supporta tutte le funzionalità offerte da [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15).</span><span class="sxs-lookup"><span data-stu-id="215c0-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) does.</span></span>
* <span data-ttu-id="215c0-135">Non è disponibile in Linux.</span><span class="sxs-lookup"><span data-stu-id="215c0-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="215c0-136">Può causare ritardi durante la prima esecuzione dei test mentre il servizio viene attivato.</span><span class="sxs-lookup"><span data-stu-id="215c0-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="215c0-137">Personalmente, non ho mai considerato un problema avere un servizio di database in esecuzione nel computer di sviluppo e in genere consiglio di usare Developer Edition.</span><span class="sxs-lookup"><span data-stu-id="215c0-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="215c0-138">Tuttavia, LocalDB potrebbe non essere appropriato per alcuni utenti, soprattutto con computer di sviluppo meno potenti.</span><span class="sxs-lookup"><span data-stu-id="215c0-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="215c0-139">L'esecuzione di SQL Server (o qualsiasi altro sistema di database) in un contenitore Docker (o un contenitore simile) è un altro modo per evitare di eseguire il sistema di database direttamente nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="215c0-139">Running SQL Server (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="215c0-140">Approccio 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="215c0-140">Approach 2: SQLite</span></span>

<span data-ttu-id="215c0-141">EF Core verifica principalmente il provider di SQL Server eseguendolo in un'istanza di SQL Server locale.</span><span class="sxs-lookup"><span data-stu-id="215c0-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="215c0-142">Questi test eseguono decine di migliaia di query in un paio di minuti in un computer veloce.</span><span class="sxs-lookup"><span data-stu-id="215c0-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="215c0-143">Questo dimostra che l'uso del sistema di database reale può essere una soluzione efficiente.</span><span class="sxs-lookup"><span data-stu-id="215c0-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="215c0-144">È un mito che l'uso di un database più leggero sia l'unico modo per eseguire rapidamente i test.</span><span class="sxs-lookup"><span data-stu-id="215c0-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="215c0-145">Detto questo, cosa accade se per qualsiasi motivo non è possibile eseguire test in un ambiente simile al sistema di database di produzione?</span><span class="sxs-lookup"><span data-stu-id="215c0-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="215c0-146">La scelta migliore successiva consiste nell'usare qualcosa con funzionalità simili.</span><span class="sxs-lookup"><span data-stu-id="215c0-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="215c0-147">Questo significa in genere un altro database relazionale, quindi [SQLite](https://sqlite.org/index.html) rappresenta la scelta più ovvia.</span><span class="sxs-lookup"><span data-stu-id="215c0-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="215c0-148">SQLite è una scelta ottimale perché:</span><span class="sxs-lookup"><span data-stu-id="215c0-148">SQLite is a good choice because:</span></span>
* <span data-ttu-id="215c0-149">Viene eseguito in-process con l'applicazione e pertanto presenta un sovraccarico basso.</span><span class="sxs-lookup"><span data-stu-id="215c0-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="215c0-150">Usa file semplici creati automaticamente per i database e quindi non richiede la gestione del database.</span><span class="sxs-lookup"><span data-stu-id="215c0-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="215c0-151">Offre una modalità in memoria che consente di evitare persino la creazione di file.</span><span class="sxs-lookup"><span data-stu-id="215c0-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="215c0-152">Tuttavia, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="215c0-152">However, remember that:</span></span>
* <span data-ttu-id="215c0-153">SQLite inevitabilmente non supporta tutte le funzionalità del sistema di database di produzione.</span><span class="sxs-lookup"><span data-stu-id="215c0-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="215c0-154">SQLite si comporterà in modo diverso rispetto al sistema di database di produzione per alcune query.</span><span class="sxs-lookup"><span data-stu-id="215c0-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="215c0-155">Quindi, se si usa SQLite per alcuni test, assicurarsi di eseguire test anche sul sistema di database reale.</span><span class="sxs-lookup"><span data-stu-id="215c0-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="215c0-156">Vedere [Test con SQLite](xref:core/miscellaneous/testing/sqlite) per indicazioni specifiche per EF Core.</span><span class="sxs-lookup"><span data-stu-id="215c0-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span> 

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="215c0-157">Approccio 3: Database in memoria di EF Core</span><span class="sxs-lookup"><span data-stu-id="215c0-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="215c0-158">EF Core viene fornito con un database in memoria usato per i test interni di EF Core stesso.</span><span class="sxs-lookup"><span data-stu-id="215c0-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="215c0-159">Questo database in genere **non è adatto per i test di applicazioni che usano EF Core**.</span><span class="sxs-lookup"><span data-stu-id="215c0-159">This database is in general **not suitable as a substitute for testing applications that use EF Core**.</span></span> <span data-ttu-id="215c0-160">In particolare:</span><span class="sxs-lookup"><span data-stu-id="215c0-160">Specifically:</span></span>

* <span data-ttu-id="215c0-161">Non è un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="215c0-161">It is not a relational database.</span></span>
* <span data-ttu-id="215c0-162">Non supporta le transazioni.</span><span class="sxs-lookup"><span data-stu-id="215c0-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="215c0-163">Non è possibile eseguire query SQL non elaborate.</span><span class="sxs-lookup"><span data-stu-id="215c0-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="215c0-164">Non è ottimizzato per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="215c0-164">It is not optimized for performance.</span></span>

<span data-ttu-id="215c0-165">Nessuno di questi aspetti è di particolare importanza quando si eseguono test dei meccanismi interni di EF Core, perché questo approccio viene usato in modo specifico nei casi in cui il database non è rilevante per i test.</span><span class="sxs-lookup"><span data-stu-id="215c0-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="215c0-166">D'altra parte, questi aspetti tendono a essere molto importanti per il test di un'applicazione che usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="215c0-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="215c0-167">Unit test</span><span class="sxs-lookup"><span data-stu-id="215c0-167">Unit testing</span></span>

<span data-ttu-id="215c0-168">Si supponga di dover testare una parte della logica di business che potrebbe dover usare alcuni dati di un database, senza dover testare in modo intrinseco le interazioni del database.</span><span class="sxs-lookup"><span data-stu-id="215c0-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="215c0-169">Un'opzione consiste nell'usare un [duplicato di test](https://en.wikipedia.org/wiki/Test_double), ad esempio una simulazione o uno scenario fittizio.</span><span class="sxs-lookup"><span data-stu-id="215c0-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="215c0-170">I duplicati di test vengono usati a Microsoft per i test interni di EF Core.</span><span class="sxs-lookup"><span data-stu-id="215c0-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="215c0-171">Tuttavia, non viene mai tentata la simulazione di DbContext o IQueryable,</span><span class="sxs-lookup"><span data-stu-id="215c0-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="215c0-172">perché si tratta di un'operazione difficile, scomoda e fragile.</span><span class="sxs-lookup"><span data-stu-id="215c0-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="215c0-173">**Evitare di farlo.**</span><span class="sxs-lookup"><span data-stu-id="215c0-173">**Don't do it.**</span></span>

<span data-ttu-id="215c0-174">Per i testing unità che usano DbContext viene invece usato il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="215c0-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="215c0-175">In questo caso, l'uso del database in memoria EF è appropriato perché il test non dipende dal comportamento del database.</span><span class="sxs-lookup"><span data-stu-id="215c0-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="215c0-176">Semplicemente evitare questo approccio per testare le query o gli aggiornamenti di database effettivi.</span><span class="sxs-lookup"><span data-stu-id="215c0-176">Just don't do this to test actual database queries or updates.</span></span>   

<span data-ttu-id="215c0-177">In [Esempio di test di EF Core](xref:core/miscellaneous/testing/testing-sample) vengono presentati alcuni test che usano il database in memoria EF, nonché SQL Server e SQLite.</span><span class="sxs-lookup"><span data-stu-id="215c0-177">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span> 
