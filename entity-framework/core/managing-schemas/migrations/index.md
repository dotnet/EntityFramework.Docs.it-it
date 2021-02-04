---
title: Panoramica delle migrazioni - EF Core
description: Panoramica dell'uso delle migrazioni per gestire gli schemi del database con Entity Framework Core
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "95003395"
---
# <a name="migrations-overview"></a><span data-ttu-id="3ac4a-103">Panoramica delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="3ac4a-103">Migrations Overview</span></span>

<span data-ttu-id="3ac4a-104">Nei progetti reali i modelli di dati cambiano quando vengono implementate le funzionalità. Vengono aggiunte o rimosse nuove entità o proprietà e conseguentemente gli schemi del database devono essere modificati per essere sincronizzati con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-104">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="3ac4a-105">La funzionalità delle migrazioni in EF Core rappresenta un metodo per l'aggiornamento incrementale dello schema del database per mantenere quest'ultimo sincronizzato con il modello di dati dell'applicazione mantenendo i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="3ac4a-106">A livello generale, le migrazioni funzionano nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-106">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="3ac4a-107">Quando viene introdotta una modifica del modello di dati, lo sviluppatore usa gli strumenti di EF Core per aggiungere una migrazione corrispondente in cui sono descritti gli aggiornamenti necessari per la sincronizzazione dello schema del database. EF Core confronta il modello corrente con uno snapshot del modello precedente per determinare le differenze e genera i file di origine della migrazione. È possibile tenere traccia dei file nel controllo del codice sorgente del progetto, come per qualsiasi altro file di origine.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-107">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="3ac4a-108">Dopo aver generato una nuova migrazione, è possibile applicarla a un database in vari modi.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-108">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="3ac4a-109">EF Core registra tutte le migrazioni applicate in una tabella di cronologia speciale, consentendo di individuare quali migrazioni sono state applicate e quali non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-109">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="3ac4a-110">La parte restante di questa pagina contiene istruzioni dettagliate per principianti sull'uso delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-110">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="3ac4a-111">Per informazioni più dettagliate, leggere le altre pagine di questa sezione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-111">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="3ac4a-112">Introduzione</span><span class="sxs-lookup"><span data-stu-id="3ac4a-112">Getting started</span></span>

<span data-ttu-id="3ac4a-113">Si supponga di aver appena completato la prima applicazione EF Core contenente il semplice modello seguente:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-113">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="3ac4a-114">Durante lo sviluppo, è possibile che siano state usate le [API di creazione ed eliminazione](xref:core/managing-schemas/ensure-created) per eseguire un'iterazione rapida, modificando il modello in base alle esigenze. Tuttavia, ora che l'applicazione è in fase di produzione, è necessario un modo per evolvere in modo sicuro lo schema senza eliminare l'intero database.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-114">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="3ac4a-115">Installare gli strumenti</span><span class="sxs-lookup"><span data-stu-id="3ac4a-115">Install the tools</span></span>

<span data-ttu-id="3ac4a-116">Per prima cosa, è necessario installare gli [strumenti da riga di comando di EF Core](xref:core/cli/index):</span><span class="sxs-lookup"><span data-stu-id="3ac4a-116">First, you'll have to install the [EF Core command-line tools](xref:core/cli/index):</span></span>

* <span data-ttu-id="3ac4a-117">È in genere consigliabile usare gli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/cli/dotnet), che funzionano su tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-117">We generally recommend using the [.NET Core CLI tools](xref:core/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="3ac4a-118">Se si preferisce usare Visual Studio o si ha esperienza con le migrazioni di EF6, è anche possibile usare gli [strumenti della console di Gestione pacchetti](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="3ac4a-118">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="3ac4a-119">Creare la prima migrazione</span><span class="sxs-lookup"><span data-stu-id="3ac4a-119">Create your first migration</span></span>

<span data-ttu-id="3ac4a-120">A questo punto si è pronti per aggiungere la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-120">You're now ready to add your first migration!</span></span> <span data-ttu-id="3ac4a-121">Chiedere a EF Core di creare una migrazione denominata **InitialCreate**:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-121">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="3ac4a-122">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac4a-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="3ac4a-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ac4a-123">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="3ac4a-124">EF Core creerà una directory denominata **Migrazioni** nel progetto e genererà alcuni file.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-124">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="3ac4a-125">È consigliabile esaminare gli elementi generati da EF Core ed eventualmente modificarli. Per il momento si ignorerà questo passaggio.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-125">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="3ac4a-126">Creare il database e lo schema</span><span class="sxs-lookup"><span data-stu-id="3ac4a-126">Create your database and schema</span></span>

<span data-ttu-id="3ac4a-127">A questo punto EF può creare il database e lo schema dalla migrazione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-127">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="3ac4a-128">È possibile eseguire questa operazione nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-128">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="3ac4a-129">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac4a-129">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="3ac4a-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ac4a-130">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

***

<span data-ttu-id="3ac4a-131">Tutto qui. L'applicazione è pronta per essere eseguita nel nuovo database e non è stato necessario scrivere una singola riga di SQL.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-131">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="3ac4a-132">Si noti che questo modo di applicare le migrazioni è ideale per lo sviluppo locale, ma è meno adatto per gli ambienti di produzione. Per altre informazioni, vedere la pagina [Applicazione delle migrazioni](xref:core/managing-schemas/migrations/applying).</span><span class="sxs-lookup"><span data-stu-id="3ac4a-132">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="3ac4a-133">Evoluzione del modello</span><span class="sxs-lookup"><span data-stu-id="3ac4a-133">Evolving your model</span></span>

<span data-ttu-id="3ac4a-134">Sono passati alcuni giorni e viene chiesto di aggiungere un timestamp di creazione ai blog.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-134">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="3ac4a-135">Sono state apportate le modifiche necessarie all'applicazione e il modello è ora simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-135">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="3ac4a-136">Il modello e il database di produzione non sono ora sincronizzati. È necessario aggiungere una nuova colonna allo schema del database.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-136">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="3ac4a-137">Verrà ora creata una nuova migrazione a tale scopo:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-137">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="3ac4a-138">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac4a-138">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="3ac4a-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ac4a-139">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="3ac4a-140">Si noti che alla migrazione viene assegnato un nome descrittivo in modo da facilitare poi la comprensione della cronologia del progetto.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-140">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="3ac4a-141">Poiché non si tratta della prima migrazione del progetto, EF Core ora confronta il modello aggiornato con uno snapshot del modello precedente (prima che fosse aggiunta la colonna). Lo snapshot del modello è uno dei file generati da EF Core in fase di aggiunta di una migrazione e viene archiviato nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-141">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="3ac4a-142">In base a tale confronto, EF Core rileva che è stata aggiunta una colonna e aggiunge la migrazione appropriata.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-142">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="3ac4a-143">È ora possibile applicare la migrazione come eseguito in precedenza:</span><span class="sxs-lookup"><span data-stu-id="3ac4a-143">You can now apply your migration as before:</span></span>

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[<span data-ttu-id="3ac4a-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac4a-144">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="3ac4a-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ac4a-145">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

***

<span data-ttu-id="3ac4a-146">Si noti che questa volta EF rileva che il database esiste già.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-146">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="3ac4a-147">Quando è stata applicata la prima migrazione, questa operazione è stata anche registrata in una tabella di cronologia delle migrazioni speciale all'interno del database. Si consente così a EF di applicare automaticamente solo la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-147">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="excluding-parts-of-your-model"></a><span data-ttu-id="3ac4a-148">Esclusione di parti del modello</span><span class="sxs-lookup"><span data-stu-id="3ac4a-148">Excluding parts of your model</span></span>

> [!NOTE]
> <span data-ttu-id="3ac4a-149">Questa funzionalità è stata introdotta in EF in Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-149">This feature was introduced EF in Core 5.0.</span></span>

<span data-ttu-id="3ac4a-150">In alcuni casi può essere utile fare riferimento ai tipi da un altro DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-150">Sometimes you may want to reference types from another DbContext.</span></span> <span data-ttu-id="3ac4a-151">Questo può causare conflitti di migrazione.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-151">This can lead to migration conflicts.</span></span> <span data-ttu-id="3ac4a-152">Per evitare questo problema, escludere il tipo dalle migrazioni di uno dei oggetti DbContext.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-152">To prevent this, exclude the type from the migrations of one of the DbContexts.</span></span>

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a><span data-ttu-id="3ac4a-153">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3ac4a-153">Next steps</span></span>

<span data-ttu-id="3ac4a-154">Sopra è stata riportata solo una breve introduzione alle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-154">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="3ac4a-155">Leggere le altre pagine della documentazione per informazioni dettagliate sulla [gestione delle migrazioni](xref:core/managing-schemas/migrations/managing), sulla relativa [applicazione](xref:core/managing-schemas/migrations/applying) e su altri aspetti.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-155">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="3ac4a-156">Il [riferimento allo strumento dell'interfaccia della riga di comando di .NET Core](xref:core/cli/index) contiene anche informazioni utili sui diversi comandi</span><span class="sxs-lookup"><span data-stu-id="3ac4a-156">The [.NET Core CLI tool reference](xref:core/cli/index) also contains useful information on the different commands</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ac4a-157">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3ac4a-157">Additional resources</span></span>

* <span data-ttu-id="3ac4a-158">[EF Core sessione standup della community](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) passa alle nuove funzionalità di migrazione in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="3ac4a-158">[EF Core Community Standup session](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) going over new migration features in EF Core 5.0.</span></span>
