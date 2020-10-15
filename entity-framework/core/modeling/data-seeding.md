---
title: Seeding dei dati-EF Core
description: Uso del seeding dei dati per popolare un database con un set di dati iniziale usando Entity Framework Core
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: f8284d7473c6fbcafde25d01ec0709c2d50b666f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063699"
---
# <a name="data-seeding"></a><span data-ttu-id="ffabd-103">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="ffabd-103">Data Seeding</span></span>

<span data-ttu-id="ffabd-104">Il seeding dei dati è il processo di popolamento di un database con un set di dati iniziale.</span><span class="sxs-lookup"><span data-stu-id="ffabd-104">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="ffabd-105">Questa operazione può essere eseguita in diversi modi EF Core:</span><span class="sxs-lookup"><span data-stu-id="ffabd-105">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="ffabd-106">Dati di inizializzazione del modello</span><span class="sxs-lookup"><span data-stu-id="ffabd-106">Model seed data</span></span>
* <span data-ttu-id="ffabd-107">Personalizzazione manuale della migrazione</span><span class="sxs-lookup"><span data-stu-id="ffabd-107">Manual migration customization</span></span>
* <span data-ttu-id="ffabd-108">Logica di inizializzazione personalizzata</span><span class="sxs-lookup"><span data-stu-id="ffabd-108">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="ffabd-109">Dati di inizializzazione del modello</span><span class="sxs-lookup"><span data-stu-id="ffabd-109">Model seed data</span></span>

<span data-ttu-id="ffabd-110">Diversamente da EF6, in EF Core, il seeding dei dati può essere associato a un tipo di entità come parte della configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="ffabd-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="ffabd-111">Le [migrazioni](xref:core/managing-schemas/migrations/index) EF core possono quindi calcolare automaticamente le operazioni di inserimento, aggiornamento o eliminazione da applicare durante l'aggiornamento del database a una nuova versione del modello.</span><span class="sxs-lookup"><span data-stu-id="ffabd-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="ffabd-112">Per determinare l'operazione da eseguire per ottenere i dati di inizializzazione nello stato desiderato, le migrazioni considerano solo le modifiche al modello.</span><span class="sxs-lookup"><span data-stu-id="ffabd-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="ffabd-113">Pertanto, le eventuali modifiche apportate ai dati eseguiti al di fuori delle migrazioni potrebbero andare perse o causare un errore.</span><span class="sxs-lookup"><span data-stu-id="ffabd-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="ffabd-114">Ad esempio, vengono configurati i dati di inizializzazione per un `Blog` in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="ffabd-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="ffabd-115">Per aggiungere entità che hanno una relazione, è necessario specificare i valori di chiave esterna:</span><span class="sxs-lookup"><span data-stu-id="ffabd-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="ffabd-116">Se il tipo di entità dispone di proprietà nello stato di ombreggiatura, è possibile usare una classe anonima per fornire i valori:</span><span class="sxs-lookup"><span data-stu-id="ffabd-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="ffabd-117">I tipi di entità di proprietà possono essere sottoposte a seeding in modo analogo:</span><span class="sxs-lookup"><span data-stu-id="ffabd-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="ffabd-118">Per ulteriori informazioni sul contesto, vedere il [progetto di esempio completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) .</span><span class="sxs-lookup"><span data-stu-id="ffabd-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="ffabd-119">Una volta aggiunti i dati al modello, è necessario utilizzare le [migrazioni](xref:core/managing-schemas/migrations/index) per applicare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="ffabd-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="ffabd-120">Se è necessario applicare migrazioni come parte di una distribuzione automatica, è possibile [creare uno script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) che può essere visualizzato in anteprima prima dell'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ffabd-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="ffabd-121">In alternativa, è possibile utilizzare `context.Database.EnsureCreated()` per creare un nuovo database contenente i dati di inizializzazione, ad esempio per un database di prova o quando si utilizza il provider in memoria o qualsiasi database non relazionale.</span><span class="sxs-lookup"><span data-stu-id="ffabd-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="ffabd-122">Si noti che se il database esiste già, `EnsureCreated()` non aggiornerà lo schema né i dati di inizializzazione nel database.</span><span class="sxs-lookup"><span data-stu-id="ffabd-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="ffabd-123">Per i database relazionali non è necessario chiamare `EnsureCreated()` se si prevede di utilizzare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="ffabd-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="ffabd-124">Limitazioni dei dati di inizializzazione del modello</span><span class="sxs-lookup"><span data-stu-id="ffabd-124">Limitations of model seed data</span></span>

<span data-ttu-id="ffabd-125">Questo tipo di dati di inizializzazione è gestito dalle migrazioni e lo script per aggiornare i dati già presenti nel database deve essere generato senza connettersi al database.</span><span class="sxs-lookup"><span data-stu-id="ffabd-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="ffabd-126">Questa operazione impone alcune restrizioni:</span><span class="sxs-lookup"><span data-stu-id="ffabd-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="ffabd-127">Il valore della chiave primaria deve essere specificato anche se viene in genere generato dal database.</span><span class="sxs-lookup"><span data-stu-id="ffabd-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="ffabd-128">Verrà usato per rilevare le modifiche dei dati tra le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="ffabd-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="ffabd-129">Se la chiave primaria viene modificata in qualsiasi modo, i dati con seeding in precedenza verranno rimossi.</span><span class="sxs-lookup"><span data-stu-id="ffabd-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="ffabd-130">Pertanto questa funzionalità è particolarmente utile per i dati statici che non si prevede di modificare al di fuori delle migrazioni e non dipende da altri elementi nel database, ad esempio i codici postali.</span><span class="sxs-lookup"><span data-stu-id="ffabd-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="ffabd-131">Se lo scenario include uno degli elementi seguenti, è consigliabile usare la logica di inizializzazione personalizzata descritta nell'ultima sezione:</span><span class="sxs-lookup"><span data-stu-id="ffabd-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="ffabd-132">Dati temporanei per il test</span><span class="sxs-lookup"><span data-stu-id="ffabd-132">Temporary data for testing</span></span>
* <span data-ttu-id="ffabd-133">Dati che dipendono dallo stato del database</span><span class="sxs-lookup"><span data-stu-id="ffabd-133">Data that depends on database state</span></span>
* <span data-ttu-id="ffabd-134">Dati che richiedono la generazione di valori di chiave da parte del database, incluse le entità che utilizzano chiavi alternative come identità</span><span class="sxs-lookup"><span data-stu-id="ffabd-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="ffabd-135">Dati che richiedono una trasformazione personalizzata (non gestita da [conversioni di valori](xref:core/modeling/value-conversions)), ad esempio un hash delle password</span><span class="sxs-lookup"><span data-stu-id="ffabd-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="ffabd-136">Dati che richiedono chiamate a un'API esterna, ad esempio ASP.NET Core ruoli di identità e creazione di utenti</span><span class="sxs-lookup"><span data-stu-id="ffabd-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="ffabd-137">Personalizzazione manuale della migrazione</span><span class="sxs-lookup"><span data-stu-id="ffabd-137">Manual migration customization</span></span>

<span data-ttu-id="ffabd-138">Quando viene aggiunta una migrazione, le modifiche apportate ai dati specificati con `HasData` vengono trasformate in chiamate a `InsertData()` , `UpdateData()` e `DeleteData()` .</span><span class="sxs-lookup"><span data-stu-id="ffabd-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="ffabd-139">Un modo per aggirare alcune delle limitazioni di `HasData` consiste nell'aggiungere manualmente queste chiamate o [operazioni personalizzate](xref:core/managing-schemas/migrations/operations) alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="ffabd-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="ffabd-140">Logica di inizializzazione personalizzata</span><span class="sxs-lookup"><span data-stu-id="ffabd-140">Custom initialization logic</span></span>

<span data-ttu-id="ffabd-141">Un modo semplice e potente per eseguire il seeding dei dati consiste nell'usare prima che venga [`DbContext.SaveChanges()`](xref:core/saving/index) avviata l'esecuzione della logica dell'applicazione principale.</span><span class="sxs-lookup"><span data-stu-id="ffabd-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="ffabd-142">Il codice di seeding non deve far parte della normale esecuzione dell'app, perché ciò può causare problemi di concorrenza quando sono in esecuzione più istanze e richiede anche che l'app disponga dell'autorizzazione per modificare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="ffabd-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="ffabd-143">A seconda dei vincoli della distribuzione, il codice di inizializzazione può essere eseguito in modi diversi:</span><span class="sxs-lookup"><span data-stu-id="ffabd-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="ffabd-144">Esecuzione locale dell'app di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="ffabd-144">Running the initialization app locally</span></span>
* <span data-ttu-id="ffabd-145">Distribuire l'app di inizializzazione con l'app principale, richiamando la routine di inizializzazione e disabilitando o rimuovendo l'app di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="ffabd-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="ffabd-146">Questo può essere in genere automatizzato tramite i [profili di pubblicazione](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="ffabd-146">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
