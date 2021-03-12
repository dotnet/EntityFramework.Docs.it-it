---
title: Esempio di test di EF Core-EF Core
description: Esempio che illustra come testare le applicazioni che usano Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: a1c858333ee8ebe5a0ad82358c518c86576cdd32
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023458"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="aff06-103">Esempio di test di EF Core</span><span class="sxs-lookup"><span data-stu-id="aff06-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="aff06-104">Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="aff06-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="aff06-105">Si noti che alcuni di questi test **dovrebbero avere esito negativo**.</span><span class="sxs-lookup"><span data-stu-id="aff06-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="aff06-106">I motivi di questo problema sono descritti di seguito.</span><span class="sxs-lookup"><span data-stu-id="aff06-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="aff06-107">Questo documento illustra un esempio per il test del codice che usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="aff06-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="aff06-108">Applicazione</span><span class="sxs-lookup"><span data-stu-id="aff06-108">The application</span></span>

<span data-ttu-id="aff06-109">L' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) contiene due progetti:</span><span class="sxs-lookup"><span data-stu-id="aff06-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>

- <span data-ttu-id="aff06-110">ItemsWebApi: un' [API Web molto semplice supportata da ASP.NET Core](/aspnet/core/tutorials/first-web-api) con un singolo controller</span><span class="sxs-lookup"><span data-stu-id="aff06-110">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="aff06-111">Test: un progetto di test [xUnit](https://xunit.net/) per testare il controller</span><span class="sxs-lookup"><span data-stu-id="aff06-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="aff06-112">Modello e regole business</span><span class="sxs-lookup"><span data-stu-id="aff06-112">The model and business rules</span></span>

<span data-ttu-id="aff06-113">Il modello che supporta questa API è costituito da due tipi di entità: Items e Tags .</span><span class="sxs-lookup"><span data-stu-id="aff06-113">The model backing this API has two entity types: Items and Tags.</span></span>

- <span data-ttu-id="aff06-114">Items hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags .</span><span class="sxs-lookup"><span data-stu-id="aff06-114">Items have a case-sensitive name and a collection of Tags.</span></span>
- <span data-ttu-id="aff06-115">Ogni Tag ha un'etichetta e un conteggio che rappresenta il numero di volte in cui è stato applicato a Item .</span><span class="sxs-lookup"><span data-stu-id="aff06-115">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
- <span data-ttu-id="aff06-116">Ogni Item deve avere un solo Tag oggetto con un'etichetta specificata.</span><span class="sxs-lookup"><span data-stu-id="aff06-116">Each Item should only have one Tag with a given label.</span></span>
  - <span data-ttu-id="aff06-117">Se un elemento viene contrassegnato con la stessa etichetta più di una volta, il conteggio del tag esistente con tale etichetta viene incrementato al posto di un nuovo tag creato.</span><span class="sxs-lookup"><span data-stu-id="aff06-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="aff06-118">L'eliminazione di un oggetto Item deve eliminare tutti gli associati Tags .</span><span class="sxs-lookup"><span data-stu-id="aff06-118">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="aff06-119">ItemTipo di entità</span><span class="sxs-lookup"><span data-stu-id="aff06-119">The Item entity type</span></span>

<span data-ttu-id="aff06-120">`Item`Tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="aff06-120">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="aff06-121">E la relativa configurazione in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="aff06-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="aff06-122">Si noti che il tipo di entità vincola il modo in cui può essere usato per riflettere il modello di dominio e le regole business.</span><span class="sxs-lookup"><span data-stu-id="aff06-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="aff06-123">In particolare:</span><span class="sxs-lookup"><span data-stu-id="aff06-123">In particular:</span></span>

- <span data-ttu-id="aff06-124">La chiave primaria viene mappata direttamente al `_id` campo e non esposta pubblicamente</span><span class="sxs-lookup"><span data-stu-id="aff06-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="aff06-125">EF rileva e usa il costruttore privato che accetta il nome e il valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="aff06-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="aff06-126">La `Name` proprietà è di sola lettura e viene impostata solo nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="aff06-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="aff06-127">Tags vengono esposti come `IReadOnlyList<Tag>` per impedire la modifica arbitraria.</span><span class="sxs-lookup"><span data-stu-id="aff06-127">Tags are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="aff06-128">EF associa la `Tags` proprietà al `_tags` campo sottostante mediante la corrispondenza dei nomi.</span><span class="sxs-lookup"><span data-stu-id="aff06-128">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span>
  - <span data-ttu-id="aff06-129">Il `AddTag` metodo accetta un'etichetta tag e implementa la regola business descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="aff06-129">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="aff06-130">Ovvero, viene aggiunto un tag solo per le nuove etichette.</span><span class="sxs-lookup"><span data-stu-id="aff06-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="aff06-131">In caso contrario, il conteggio su un'etichetta esistente viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="aff06-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="aff06-132">La `Tags` proprietà di navigazione è configurata per una relazione molti-a-uno</span><span class="sxs-lookup"><span data-stu-id="aff06-132">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="aff06-133">Non è necessario disporre di una proprietà di navigazione da Tag a Item , pertanto non è inclusa.</span><span class="sxs-lookup"><span data-stu-id="aff06-133">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="aff06-134">Inoltre, non Tag definisce una proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="aff06-134">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="aff06-135">Al contrario, EF creerà e gestirà una proprietà in stato shadow.</span><span class="sxs-lookup"><span data-stu-id="aff06-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="aff06-136">TagTipo di entità</span><span class="sxs-lookup"><span data-stu-id="aff06-136">The Tag entity type</span></span>

<span data-ttu-id="aff06-137">`Tag`Tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="aff06-137">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="aff06-138">E la relativa configurazione in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="aff06-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="aff06-139">In modo analogo a Item , Tag nasconde la chiave primaria e rende la proprietà di sola `Label` lettura.</span><span class="sxs-lookup"><span data-stu-id="aff06-139">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="aff06-140">Il Items controller</span><span class="sxs-lookup"><span data-stu-id="aff06-140">The ItemsController</span></span>

<span data-ttu-id="aff06-141">Il controller API Web è piuttosto semplice.</span><span class="sxs-lookup"><span data-stu-id="aff06-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="aff06-142">Ottiene un oggetto `DbContext` dal contenitore di inserimento delle dipendenze tramite l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="aff06-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="aff06-143">Dispone di metodi per ottenere tutti Items o un oggetto Item con un nome specificato:</span><span class="sxs-lookup"><span data-stu-id="aff06-143">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="aff06-144">Dispone di un metodo per aggiungere un nuovo Item :</span><span class="sxs-lookup"><span data-stu-id="aff06-144">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="aff06-145">Metodo per contrassegnare un oggetto Item con un'etichetta:</span><span class="sxs-lookup"><span data-stu-id="aff06-145">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="aff06-146">E un metodo per eliminare un oggetto Item e tutti gli associati Tags :</span><span class="sxs-lookup"><span data-stu-id="aff06-146">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="aff06-147">La maggior parte della convalida e della gestione degli errori sono state rimosse per ridurre il disordine.</span><span class="sxs-lookup"><span data-stu-id="aff06-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="aff06-148">Test</span><span class="sxs-lookup"><span data-stu-id="aff06-148">The Tests</span></span>

<span data-ttu-id="aff06-149">I test sono organizzati per l'esecuzione con più configurazioni del provider di database:</span><span class="sxs-lookup"><span data-stu-id="aff06-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="aff06-150">Provider di SQL Server, che è il provider utilizzato dall'applicazione</span><span class="sxs-lookup"><span data-stu-id="aff06-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="aff06-151">Provider SQLite</span><span class="sxs-lookup"><span data-stu-id="aff06-151">The SQLite provider</span></span>
- <span data-ttu-id="aff06-152">Provider SQLite che usa i database SQLite in memoria</span><span class="sxs-lookup"><span data-stu-id="aff06-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="aff06-153">Provider di database in memoria EF</span><span class="sxs-lookup"><span data-stu-id="aff06-153">The EF in-memory database provider</span></span>

<span data-ttu-id="aff06-154">Questa operazione viene eseguita inserendo tutti i test in una classe di base, quindi ereditando da questo oggetto per eseguire il test con ogni provider.</span><span class="sxs-lookup"><span data-stu-id="aff06-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="aff06-155">Se non si usa local DB, sarà necessario modificare la stringa di connessione SQL Server.</span><span class="sxs-lookup"><span data-stu-id="aff06-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="aff06-156">Vedere [test con SQLite](xref:core/testing/sqlite) per istruzioni sull'uso di SQLite per i test in memoria.</span><span class="sxs-lookup"><span data-stu-id="aff06-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="aff06-157">I due test seguenti dovrebbero avere esito negativo:</span><span class="sxs-lookup"><span data-stu-id="aff06-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="aff06-158">`Can_remove_item_and_all_associated_tags` Quando si esegue con il provider di database in memoria EF</span><span class="sxs-lookup"><span data-stu-id="aff06-158">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="aff06-159">`Can_add_item_differing_only_by_case` Quando si esegue con il provider di SQL Server</span><span class="sxs-lookup"><span data-stu-id="aff06-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="aff06-160">Questo aspetto viene trattato in modo più dettagliato di seguito.</span><span class="sxs-lookup"><span data-stu-id="aff06-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="aff06-161">Impostazione e seeding del database</span><span class="sxs-lookup"><span data-stu-id="aff06-161">Setting up and seeding the database</span></span>

<span data-ttu-id="aff06-162">XUnit, come la maggior parte dei framework di test, creerà una nuova istanza della classe di test per ogni esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="aff06-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="aff06-163">Inoltre, XUnit non eseguirà i test in una determinata classe di test in parallelo.</span><span class="sxs-lookup"><span data-stu-id="aff06-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="aff06-164">Ciò significa che è possibile impostare e configurare il database nel costruttore di test e che sarà in uno stato noto per ogni test.</span><span class="sxs-lookup"><span data-stu-id="aff06-164">This means that we can set up and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="aff06-165">Questo esempio ricrea il database per ogni test.</span><span class="sxs-lookup"><span data-stu-id="aff06-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="aff06-166">Questo approccio funziona bene per i test di database SQLite e EF in memoria, ma può comportare un sovraccarico significativo con altri sistemi di database, tra cui SQL Server.</span><span class="sxs-lookup"><span data-stu-id="aff06-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="aff06-167">Gli approcci per la riduzione di questo overhead sono trattati nella [condivisione dei database tra i test](xref:core/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="aff06-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="aff06-168">Quando ogni test viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="aff06-168">When each test is run:</span></span>

- <span data-ttu-id="aff06-169">DbContextOptions sono configurati per il provider in uso e passati al costruttore della classe base</span><span class="sxs-lookup"><span data-stu-id="aff06-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="aff06-170">Queste opzioni vengono archiviate in una proprietà e utilizzate in tutti i test per la creazione di istanze di DbContext</span><span class="sxs-lookup"><span data-stu-id="aff06-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="aff06-171">Viene chiamato un metodo di inizializzazione per creare ed eseguire il seeding del database</span><span class="sxs-lookup"><span data-stu-id="aff06-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="aff06-172">Il metodo Seed garantisce che il database sia pulito e quindi ricreandolo</span><span class="sxs-lookup"><span data-stu-id="aff06-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="aff06-173">Alcune entità di test note vengono create e salvate nel database</span><span class="sxs-lookup"><span data-stu-id="aff06-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="aff06-174">Ogni classe di test concreta eredita quindi da questa.</span><span class="sxs-lookup"><span data-stu-id="aff06-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="aff06-175">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="aff06-175">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="aff06-176">Struttura di test</span><span class="sxs-lookup"><span data-stu-id="aff06-176">Test structure</span></span>

<span data-ttu-id="aff06-177">Anche se l'applicazione usa l'inserimento delle dipendenze, i test non lo eseguono.</span><span class="sxs-lookup"><span data-stu-id="aff06-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="aff06-178">Per usare l'inserimento delle dipendenze, tuttavia, il codice aggiuntivo richiesto ha un valore minimo.</span><span class="sxs-lookup"><span data-stu-id="aff06-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="aff06-179">Viene invece creato un DbContext utilizzando `new` e quindi passato direttamente come dipendenza al controller.</span><span class="sxs-lookup"><span data-stu-id="aff06-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="aff06-180">Ogni test esegue quindi il metodo sottoposto a test sul controller e dichiara i risultati come previsto.</span><span class="sxs-lookup"><span data-stu-id="aff06-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="aff06-181">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="aff06-181">For example:</span></span>

[!code-csharp[CanGetItems](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="aff06-182">Si noti che per eseguire il seeding del database e per eseguire i test vengono usate istanze DbContext diverse.</span><span class="sxs-lookup"><span data-stu-id="aff06-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="aff06-183">In questo modo si garantisce che il test non stia usando (o inciampare) le entità rilevate dal contesto durante il seeding.</span><span class="sxs-lookup"><span data-stu-id="aff06-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="aff06-184">Corrisponde inoltre a ciò che accade in app Web e servizi.</span><span class="sxs-lookup"><span data-stu-id="aff06-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="aff06-185">I test che mutano il database creano una seconda istanza di DbContext nel test per motivi analoghi.</span><span class="sxs-lookup"><span data-stu-id="aff06-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="aff06-186">Ovvero creando un nuovo contesto pulito e quindi leggendolo dal database per assicurarsi che le modifiche siano state salvate nel database.</span><span class="sxs-lookup"><span data-stu-id="aff06-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="aff06-187">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="aff06-187">For example:</span></span>

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="aff06-188">Due test leggermente più necessari coprono la logica di business per l'aggiunta di tags .</span><span class="sxs-lookup"><span data-stu-id="aff06-188">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="aff06-189">Problemi relativi all'utilizzo di provider di database diversi</span><span class="sxs-lookup"><span data-stu-id="aff06-189">Issues using different database providers</span></span>

<span data-ttu-id="aff06-190">Il test con un sistema di database diverso da quello usato nell'applicazione di produzione può causare problemi.</span><span class="sxs-lookup"><span data-stu-id="aff06-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="aff06-191">Questi sono trattati a livello concettuale nel [codice di test che usa EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="aff06-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="aff06-192">Le sezioni seguenti illustrano due esempi di problemi che si verificano nei test di questo esempio.</span><span class="sxs-lookup"><span data-stu-id="aff06-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="aff06-193">Test superato quando l'applicazione è interruppe</span><span class="sxs-lookup"><span data-stu-id="aff06-193">Test passes when the application is broken</span></span>

<span data-ttu-id="aff06-194">Uno dei requisiti per l'applicazione è che " Items hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags ".</span><span class="sxs-lookup"><span data-stu-id="aff06-194">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="aff06-195">Questo è piuttosto semplice da testare:</span><span class="sxs-lookup"><span data-stu-id="aff06-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="aff06-196">L'esecuzione di questo test nel database in memoria EF indica che tutto funziona correttamente.</span><span class="sxs-lookup"><span data-stu-id="aff06-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="aff06-197">Tutto sembra corretto quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="aff06-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="aff06-198">Tuttavia, il test ha esito negativo quando viene eseguito su SQL Server.</span><span class="sxs-lookup"><span data-stu-id="aff06-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="aff06-199">Ciò è dovuto al fatto che il database in memoria EF e il database SQLite fanno distinzione tra maiuscole e minuscole per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="aff06-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="aff06-200">SQL Server, d'altra parte, non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="aff06-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="aff06-201">EF Core, da progettazione, non modifica questi comportamenti perché la forzatura di una modifica con distinzione maiuscole/minuscole può avere un notevole effetto sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="aff06-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="aff06-202">Quando sappiamo che questo è un problema, possiamo correggere l'applicazione e compensare i test.</span><span class="sxs-lookup"><span data-stu-id="aff06-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="aff06-203">Tuttavia, il punto in questo caso è che questo bug può essere ignorato se si verifica solo con il database in memoria EF o con i provider SQLite.</span><span class="sxs-lookup"><span data-stu-id="aff06-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="aff06-204">Il test ha esito negativo quando l'applicazione è corretta</span><span class="sxs-lookup"><span data-stu-id="aff06-204">Test fails when the application is correct</span></span>

<span data-ttu-id="aff06-205">Un altro requisito per l'applicazione è che "l'eliminazione di un Item dovrebbe eliminare tutti gli associati Tags ".</span><span class="sxs-lookup"><span data-stu-id="aff06-205">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="aff06-206">Anche in questo caso, è facile da testare:</span><span class="sxs-lookup"><span data-stu-id="aff06-206">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="aff06-207">Questo test viene superato SQL Server e SQLite, ma ha esito negativo con il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="aff06-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="aff06-208">In questo caso, l'applicazione funziona correttamente perché SQL Server supporta le [eliminazioni a catena](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="aff06-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="aff06-209">SQLite supporta inoltre le eliminazioni a catena, così come la maggior parte dei database relazionali, quindi il test di questa operazione su SQLite funziona.</span><span class="sxs-lookup"><span data-stu-id="aff06-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="aff06-210">D'altra parte, il database in memoria EF [non supporta le eliminazioni a catena](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="aff06-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="aff06-211">Questo significa che questa parte dell'applicazione non può essere testata con il provider di database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="aff06-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
