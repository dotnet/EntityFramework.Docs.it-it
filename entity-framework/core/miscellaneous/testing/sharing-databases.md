---
title: Condivisione di database tra test-EF Core
description: Esempio che illustra come condividere un database tra più test
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564280"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="e0698-103">Condivisione di database tra test</span><span class="sxs-lookup"><span data-stu-id="e0698-103">Sharing databases between tests</span></span>

<span data-ttu-id="e0698-104">Nell' [esempio EF core testing](xref:core/miscellaneous/testing/testing-sample) è stato illustrato come testare le applicazioni in diversi sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="e0698-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="e0698-105">Per questo esempio, ogni test ha creato un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="e0698-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="e0698-106">Si tratta di un modello efficace quando si usa SQLite o il database in memoria EF, ma può comportare un sovraccarico significativo quando si usano altri sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="e0698-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="e0698-107">Questo esempio si basa sull'esempio precedente spostando la creazione del database in una fixture di test.</span><span class="sxs-lookup"><span data-stu-id="e0698-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="e0698-108">Ciò consente la creazione e il seeding di un singolo database di SQL Server solo una volta per tutti i test.</span><span class="sxs-lookup"><span data-stu-id="e0698-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="e0698-109">Prima di continuare, assicurarsi di usare l' [esempio EF core testing](xref:core/miscellaneous/testing/testing-sample) .</span><span class="sxs-lookup"><span data-stu-id="e0698-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="e0698-110">Non è difficile scrivere più test sullo stesso database.</span><span class="sxs-lookup"><span data-stu-id="e0698-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="e0698-111">Il trucco viene eseguito in modo che i test non vengano eseguiti tra loro durante l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e0698-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="e0698-112">Questa operazione richiede informazioni:</span><span class="sxs-lookup"><span data-stu-id="e0698-112">This requires understanding:</span></span>
* <span data-ttu-id="e0698-113">Come condividere in modo sicuro gli oggetti tra i test</span><span class="sxs-lookup"><span data-stu-id="e0698-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="e0698-114">Quando il Framework di test esegue i test in parallelo</span><span class="sxs-lookup"><span data-stu-id="e0698-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="e0698-115">Come proteggere il database in uno stato pulito per ogni test</span><span class="sxs-lookup"><span data-stu-id="e0698-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="e0698-116">Fixture</span><span class="sxs-lookup"><span data-stu-id="e0698-116">The fixture</span></span>

<span data-ttu-id="e0698-117">Si userà una fixture di test per la condivisione degli oggetti tra i test.</span><span class="sxs-lookup"><span data-stu-id="e0698-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="e0698-118">La [documentazione di xUnit](https://xunit.net/docs/shared-context.html) indica che deve essere utilizzata una fixture "quando si desidera creare un singolo contesto di test e condividerlo tra tutti i test nella classe e quindi eseguire la pulizia al termine di tutti i test nella classe".</span><span class="sxs-lookup"><span data-stu-id="e0698-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="e0698-119">Questo esempio usa [xUnit](https://xunit.net/), ma esistono concetti simili in altri Framework di test, tra cui [NUnit](https://nunit.org/).</span><span class="sxs-lookup"><span data-stu-id="e0698-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="e0698-120">Ciò significa che è necessario spostare la creazione e il seeding del database in una classe fixture.</span><span class="sxs-lookup"><span data-stu-id="e0698-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="e0698-121">e ha l'aspetto seguente:</span><span class="sxs-lookup"><span data-stu-id="e0698-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="e0698-122">Per il momento, si noti come il costruttore:</span><span class="sxs-lookup"><span data-stu-id="e0698-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="e0698-123">Crea una singola connessione di database per la durata della fixture</span><span class="sxs-lookup"><span data-stu-id="e0698-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="e0698-124">Crea e inizializza il database chiamando il `Seed` metodo.</span><span class="sxs-lookup"><span data-stu-id="e0698-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="e0698-125">Ignora il blocco per il momento; verranno riportate in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="e0698-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="e0698-126">Non è necessario che il codice di creazione e seeding sia asincrono.</span><span class="sxs-lookup"><span data-stu-id="e0698-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="e0698-127">La sua esecuzione asincrona complica il codice e non migliora le prestazioni o la velocità effettiva dei test.</span><span class="sxs-lookup"><span data-stu-id="e0698-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="e0698-128">Il database viene creato eliminando prima qualsiasi database esistente e quindi creando un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="e0698-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="e0698-129">In questo modo si garantisce che il database corrisponda al modello EF corrente anche se è stato modificato dopo l'ultima esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="e0698-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="e0698-130">Può essere più veloce "pulire" il database esistente usando qualcosa di simile a [respawn](https://jimmybogard.com/tag/respawn/) anziché ricrearlo ogni volta.</span><span class="sxs-lookup"><span data-stu-id="e0698-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="e0698-131">È tuttavia necessario prestare attenzione per assicurarsi che lo schema del database sia aggiornato con il modello EF quando si esegue questa operazione.</span><span class="sxs-lookup"><span data-stu-id="e0698-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="e0698-132">La connessione al database viene eliminata quando la fixture viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="e0698-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="e0698-133">A questo punto è inoltre possibile prendere in considerazione l'eliminazione del database di prova.</span><span class="sxs-lookup"><span data-stu-id="e0698-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="e0698-134">Tuttavia, questa operazione richiederà un blocco aggiuntivo e un conteggio dei riferimenti se la fixture è condivisa da più classi di test.</span><span class="sxs-lookup"><span data-stu-id="e0698-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="e0698-135">Inoltre, è spesso utile fare in modo che il database di test sia ancora disponibile per il debug di test non superati.</span><span class="sxs-lookup"><span data-stu-id="e0698-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="e0698-136">Uso della fixture</span><span class="sxs-lookup"><span data-stu-id="e0698-136">Using the fixture</span></span>

<span data-ttu-id="e0698-137">XUnit dispone di un modello comune per l'associazione di una fixture di test a una classe di test:</span><span class="sxs-lookup"><span data-stu-id="e0698-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="e0698-138">XUnit creerà ora una singola istanza di fixture e la passerà a ogni istanza della classe di test.</span><span class="sxs-lookup"><span data-stu-id="e0698-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="e0698-139">(Ricordare dal primo [esempio di test](xref:core/miscellaneous/testing/testing-sample) che xUnit crea una nuova istanza della classe di test ogni volta che viene eseguito un test). Ciò significa che il database verrà creato e sottoposta a seeding una volta, quindi ogni test utilizzerà il database.</span><span class="sxs-lookup"><span data-stu-id="e0698-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="e0698-140">Si noti che i test all'interno di una singola classe non verranno eseguiti in parallelo.</span><span class="sxs-lookup"><span data-stu-id="e0698-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="e0698-141">Ciò significa che per ogni test è possibile utilizzare la stessa connessione di database, anche se l' `DbConnection` oggetto non è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e0698-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="e0698-142">Gestione dello stato del database</span><span class="sxs-lookup"><span data-stu-id="e0698-142">Maintaining database state</span></span>

<span data-ttu-id="e0698-143">I test hanno spesso la necessità di mutare i dati di test con inserimenti, aggiornamenti ed eliminazioni.</span><span class="sxs-lookup"><span data-stu-id="e0698-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="e0698-144">Queste modifiche, tuttavia, influiscano su altri test che prevedono un database pulito e con seeding.</span><span class="sxs-lookup"><span data-stu-id="e0698-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="e0698-145">Questa operazione può essere eseguita eseguendo i test di mutazione all'interno di una transazione.</span><span class="sxs-lookup"><span data-stu-id="e0698-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="e0698-146">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e0698-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="e0698-147">Si noti che la transazione viene creata quando il test viene avviato ed eliminato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="e0698-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="e0698-148">Se si elimina la transazione, ne verrà eseguito il rollback, quindi nessuna delle modifiche verrà visualizzata da altri test.</span><span class="sxs-lookup"><span data-stu-id="e0698-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="e0698-149">Il metodo helper per la creazione di un contesto (vedere il codice della fixture precedente) accetta questa transazione e sceglie il DbContext per utilizzarlo.</span><span class="sxs-lookup"><span data-stu-id="e0698-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="e0698-150">Condivisione della fixture</span><span class="sxs-lookup"><span data-stu-id="e0698-150">Sharing the fixture</span></span>

<span data-ttu-id="e0698-151">È possibile che sia stato notato il blocco del codice relativo alla creazione e al seeding del database.</span><span class="sxs-lookup"><span data-stu-id="e0698-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="e0698-152">Questa operazione non è necessaria per questo esempio perché solo una classe di test usa la fixture, quindi viene creata solo una singola istanza di fixture.</span><span class="sxs-lookup"><span data-stu-id="e0698-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="e0698-153">Tuttavia, potrebbe essere necessario usare la stessa fixture con più classi di test.</span><span class="sxs-lookup"><span data-stu-id="e0698-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="e0698-154">XUnit creerà un'istanza di fixture per ognuna di queste classi.</span><span class="sxs-lookup"><span data-stu-id="e0698-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="e0698-155">Questi possono essere usati da thread diversi che eseguono test in parallelo.</span><span class="sxs-lookup"><span data-stu-id="e0698-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="e0698-156">Pertanto, è importante avere un blocco appropriato per garantire che solo un thread faccia la creazione e il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="e0698-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="e0698-157">Qui è `lock` semplice.</span><span class="sxs-lookup"><span data-stu-id="e0698-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="e0698-158">Non è necessario tentare di eseguire operazioni più complesse, ad esempio qualsiasi modello senza blocco.</span><span class="sxs-lookup"><span data-stu-id="e0698-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
