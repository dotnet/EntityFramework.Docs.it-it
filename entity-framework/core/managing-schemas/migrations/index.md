---
title: Panoramica delle migrazioni-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238229"
---
# <a name="migrations-overview"></a><span data-ttu-id="cbac9-102">Cenni preliminari sulle migrazioni</span><span class="sxs-lookup"><span data-stu-id="cbac9-102">Migrations Overview</span></span>

<span data-ttu-id="cbac9-103">Nei progetti reali, i modelli di dati cambiano quando vengono implementate le funzionalità: le nuove entità o proprietà vengono aggiunte e rimosse e gli schemi di database devono essere modificati di conseguenza per essere mantenuti sincronizzati con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="cbac9-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="cbac9-104">La funzionalità delle migrazioni in EF Core rappresenta un metodo per l'aggiornamento incrementale dello schema del database per mantenere quest'ultimo sincronizzato con il modello di dati dell'applicazione mantenendo i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="cbac9-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="cbac9-105">A un livello elevato, le migrazioni funzionano nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="cbac9-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="cbac9-106">Quando viene introdotta una modifica del modello di dati, lo sviluppatore utilizza EF Core strumenti per aggiungere una migrazione corrispondente che descrive gli aggiornamenti necessari per la sincronizzazione dello schema del database. EF Core confronta il modello corrente con uno snapshot del modello precedente per determinare le differenze e genera i file di origine della migrazione. è possibile tenere traccia dei file nel controllo del codice sorgente del progetto, come qualsiasi altro file di origine.</span><span class="sxs-lookup"><span data-stu-id="cbac9-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="cbac9-107">Una volta generata una nuova migrazione, è possibile applicarla a un database in vari modi.</span><span class="sxs-lookup"><span data-stu-id="cbac9-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="cbac9-108">EF Core registra tutte le migrazioni applicate in una tabella di cronologia speciale, consentendo di individuare quali migrazioni sono state applicate e quali non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="cbac9-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="cbac9-109">Il resto di questa pagina è una guida dettagliata per principianti per l'uso delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="cbac9-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="cbac9-110">Consultare le altre pagine di questa sezione per informazioni più dettagliate.</span><span class="sxs-lookup"><span data-stu-id="cbac9-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="cbac9-111">Introduzione</span><span class="sxs-lookup"><span data-stu-id="cbac9-111">Getting started</span></span>

<span data-ttu-id="cbac9-112">Si supponga di aver appena completato la prima applicazione EF Core, che contiene il modello semplice seguente:</span><span class="sxs-lookup"><span data-stu-id="cbac9-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="cbac9-113">Durante lo sviluppo, è possibile che siano state utilizzate le [API Create e drop](xref:core/managing-schemas/ensure-created) per eseguire un'iterazione rapida, modificando il modello in base alle esigenze. Tuttavia, ora che l'applicazione è in fase di produzione, è necessario un modo per evolvere in modo sicuro lo schema senza eliminare l'intero database.</span><span class="sxs-lookup"><span data-stu-id="cbac9-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="cbac9-114">Installare gli strumenti</span><span class="sxs-lookup"><span data-stu-id="cbac9-114">Install the tools</span></span>

<span data-ttu-id="cbac9-115">Prima di tutto, è necessario installare gli [strumenti da riga di comando EF Core](xref:core/miscellaneous/cli/index):</span><span class="sxs-lookup"><span data-stu-id="cbac9-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="cbac9-116">È in genere consigliabile usare gli [strumenti di interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet), che funzionano su tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="cbac9-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="cbac9-117">Se si ha familiarità con l'uso di Visual Studio o si ha esperienza con le migrazioni di EF6, è anche possibile usare gli [strumenti della console di gestione pacchetti](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="cbac9-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="cbac9-118">Creazione della prima migrazione</span><span class="sxs-lookup"><span data-stu-id="cbac9-118">Create your first migration</span></span>

<span data-ttu-id="cbac9-119">A questo punto si è pronti per aggiungere la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="cbac9-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="cbac9-120">Indicare EF Core creare una migrazione denominata **InitialCreate**:</span><span class="sxs-lookup"><span data-stu-id="cbac9-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cbac9-121">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="cbac9-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cbac9-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbac9-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="cbac9-123">EF Core creerà una directory denominata **Migrations** nel progetto e genererà alcuni file.</span><span class="sxs-lookup"><span data-stu-id="cbac9-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="cbac9-124">È consigliabile esaminare le EF Core generate ed eventualmente modificarle, ma per il momento si ignorerà questo aspetto.</span><span class="sxs-lookup"><span data-stu-id="cbac9-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="cbac9-125">Creare il database e lo schema</span><span class="sxs-lookup"><span data-stu-id="cbac9-125">Create your database and schema</span></span>

<span data-ttu-id="cbac9-126">A questo punto è possibile fare in modo che EF crei il database e crei lo schema dalla migrazione.</span><span class="sxs-lookup"><span data-stu-id="cbac9-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="cbac9-127">Questa operazione può essere eseguita tramite il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="cbac9-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cbac9-128">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="cbac9-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="cbac9-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbac9-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="cbac9-130">Questo è tutto qui: l'applicazione è pronta per essere eseguita nel nuovo database e non è stato necessario scrivere una singola riga di SQL.</span><span class="sxs-lookup"><span data-stu-id="cbac9-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="cbac9-131">Si noti che questo modo di applicare le migrazioni è ideale per lo sviluppo locale, ma è meno adatto per gli ambienti di produzione. per altre informazioni, vedere la [pagina applicazione delle migrazioni](xref:core/managing-schemas/migrations/applying) .</span><span class="sxs-lookup"><span data-stu-id="cbac9-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="cbac9-132">Evoluzione del modello</span><span class="sxs-lookup"><span data-stu-id="cbac9-132">Evolving your model</span></span>

<span data-ttu-id="cbac9-133">Sono stati superati alcuni giorni e viene chiesto di aggiungere un timestamp di creazione ai Blog.</span><span class="sxs-lookup"><span data-stu-id="cbac9-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="cbac9-134">Sono state apportate le modifiche necessarie all'applicazione e il modello è ora simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="cbac9-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="cbac9-135">Il modello e il database di produzione sono ora non sincronizzati. è necessario aggiungere una nuova colonna allo schema del database.</span><span class="sxs-lookup"><span data-stu-id="cbac9-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="cbac9-136">Verrà ora creata una nuova migrazione:</span><span class="sxs-lookup"><span data-stu-id="cbac9-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cbac9-137">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="cbac9-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cbac9-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbac9-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="cbac9-139">Si noti che viene fornito un nome descrittivo per la migrazione, per semplificare la comprensione della cronologia del progetto in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="cbac9-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="cbac9-140">Poiché non si tratta della prima migrazione del progetto, EF Core ora confronta il modello aggiornato con uno snapshot del vecchio modello, prima dell'aggiunta della colonna; lo snapshot del modello è uno dei file generati da EF Core quando si aggiunge una migrazione e viene archiviato nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="cbac9-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="cbac9-141">In base a tale confronto, EF Core rileva che è stata aggiunta una colonna e aggiunge la migrazione appropriata.</span><span class="sxs-lookup"><span data-stu-id="cbac9-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="cbac9-142">È ora possibile applicare la migrazione come prima:</span><span class="sxs-lookup"><span data-stu-id="cbac9-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cbac9-143">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="cbac9-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="cbac9-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbac9-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="cbac9-145">Si noti che questa volta, EF rileva che il database esiste già.</span><span class="sxs-lookup"><span data-stu-id="cbac9-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="cbac9-146">Inoltre, quando è stata applicata la prima migrazione, questo fact è stato registrato in una tabella di cronologia delle migrazioni speciale nel database. Questo consente a EF di applicare automaticamente solo la nuova migrazione.</span><span class="sxs-lookup"><span data-stu-id="cbac9-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="cbac9-147">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="cbac9-147">Next steps</span></span>

<span data-ttu-id="cbac9-148">Sopra è stata solo una breve introduzione alle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="cbac9-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="cbac9-149">Per ulteriori informazioni sulla [gestione delle migrazioni](xref:core/managing-schemas/migrations/managing), sull' [applicazione](xref:core/managing-schemas/migrations/applying)e su altri aspetti, consultare le altre pagine della documentazione.</span><span class="sxs-lookup"><span data-stu-id="cbac9-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="cbac9-150">Il [riferimento allo strumento interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/index) contiene anche informazioni utili sui diversi comandi</span><span class="sxs-lookup"><span data-stu-id="cbac9-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
