---
title: Gestione delle migrazioni-EF Core
description: Aggiunta, rimozione e gestione delle migrazioni dello schema di database con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 81f7cec54510d95b1e2432d56ff95110224fd9bf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429852"
---
# <a name="managing-migrations"></a><span data-ttu-id="553ec-103">Gestione delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="553ec-103">Managing Migrations</span></span>

<span data-ttu-id="553ec-104">Quando il modello viene modificato, le migrazioni vengono aggiunte e rimosse come parte dello sviluppo normale e i file di migrazione vengono archiviati nel controllo del codice sorgente del progetto.</span><span class="sxs-lookup"><span data-stu-id="553ec-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="553ec-105">Per gestire le migrazioni, è necessario innanzitutto installare gli [strumenti da riga di comando EF Core](xref:core/cli/index).</span><span class="sxs-lookup"><span data-stu-id="553ec-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="553ec-106">Se `DbContext` si trova in un assembly diverso da quello del progetto di avvio, è possibile specificare in modo esplicito i progetti di destinazione e di avvio negli [strumenti della console di Gestione pacchetti](xref:core/cli/powershell#target-and-startup-project) o negli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="553ec-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="553ec-107">Aggiungere una migrazione</span><span class="sxs-lookup"><span data-stu-id="553ec-107">Add a migration</span></span>

<span data-ttu-id="553ec-108">Una volta modificato il modello, è possibile aggiungere una migrazione per la modifica:</span><span class="sxs-lookup"><span data-stu-id="553ec-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="553ec-109">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="553ec-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="553ec-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="553ec-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="553ec-111">Il nome della migrazione può essere usato come messaggio di commit in un sistema di controllo della versione.</span><span class="sxs-lookup"><span data-stu-id="553ec-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="553ec-112">Ad esempio, è possibile scegliere un nome come *AddBlogCreatedTimestamp* se la modifica è una nuova `CreatedTimestamp` proprietà nell' `Blog` entità.</span><span class="sxs-lookup"><span data-stu-id="553ec-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="553ec-113">Nella directory **Migrations** del progetto vengono aggiunti tre file:</span><span class="sxs-lookup"><span data-stu-id="553ec-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="553ec-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs** : il file principale delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="553ec-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs** --The main migrations file.</span></span> <span data-ttu-id="553ec-115">Contiene le operazioni necessarie per applicare la migrazione (in `Up`) e per ripristinarla (in `Down`).</span><span class="sxs-lookup"><span data-stu-id="553ec-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="553ec-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp. designer. cs** , il file di metadati delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="553ec-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs** --The migrations metadata file.</span></span> <span data-ttu-id="553ec-117">Contiene informazioni usate da Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="553ec-117">Contains information used by EF.</span></span>
* <span data-ttu-id="553ec-118">**MyContextModelSnapshot.cs** : snapshot del modello corrente.</span><span class="sxs-lookup"><span data-stu-id="553ec-118">**MyContextModelSnapshot.cs** --A snapshot of your current model.</span></span> <span data-ttu-id="553ec-119">Usato per determinare cosa è stato modificato durante l'aggiunta della migrazione successiva.</span><span class="sxs-lookup"><span data-stu-id="553ec-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="553ec-120">Il timestamp nel nome file consente di mantenerne l'ordine cronologico e di visualizzare quindi la successione delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="553ec-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="553ec-121">Spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="553ec-121">Namespaces</span></span>

<span data-ttu-id="553ec-122">È consentito spostare i file della cartella Migrations e modificarne lo spazio dei nomi manualmente.</span><span class="sxs-lookup"><span data-stu-id="553ec-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="553ec-123">Le nuove migrazioni vengono create come migrazioni di pari livello rispetto all'ultima.</span><span class="sxs-lookup"><span data-stu-id="553ec-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="553ec-124">In alternativa, è possibile specificare la directory in fase di generazione come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="553ec-124">Alternatively, you can specify the directory at generation time as follows:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="553ec-125">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="553ec-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> <span data-ttu-id="553ec-126">In EF Core 5,0, è anche possibile modificare lo spazio dei nomi indipendentemente dalla directory usando `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="553ec-126">In EF Core 5.0, you can also change the namespace independently of the directory using `--namespace`.</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="553ec-127">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="553ec-127">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> <span data-ttu-id="553ec-128">In EF Core 5,0, è anche possibile modificare lo spazio dei nomi indipendentemente dalla directory usando `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="553ec-128">In EF Core 5.0, you can also change the namespace independently of the directory using `-Namespace`.</span></span>

<span data-ttu-id="553ec-129">\*\*_</span><span class="sxs-lookup"><span data-stu-id="553ec-129">\*\*_</span></span>

## <a name="customize-migration-code"></a><span data-ttu-id="553ec-130">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="553ec-130">Customize migration code</span></span>

<span data-ttu-id="553ec-131">Sebbene EF Core in genere crei migrazioni accurate, è sempre necessario esaminare il codice e verificare che corrisponda alla modifica desiderata; in alcuni casi, è anche necessario eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="553ec-131">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="553ec-132">Ridenominazione colonne</span><span class="sxs-lookup"><span data-stu-id="553ec-132">Column renames</span></span>

<span data-ttu-id="553ec-133">Un esempio importante in cui è necessario personalizzare le migrazioni è quando si rinomina una proprietà.</span><span class="sxs-lookup"><span data-stu-id="553ec-133">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="553ec-134">Se ad esempio si rinomina una proprietà da `Name` a `FullName` , EF Core genererà la seguente migrazione:</span><span class="sxs-lookup"><span data-stu-id="553ec-134">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="553ec-135">EF Core in genere non è in grado di stabilire quando si intende eliminare una colonna e crearne una nuova (due modifiche separate) e quando una colonna deve essere rinominata.</span><span class="sxs-lookup"><span data-stu-id="553ec-135">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="553ec-136">Se la migrazione precedente viene applicata così come sono, tutti i nomi dei clienti andranno persi.</span><span class="sxs-lookup"><span data-stu-id="553ec-136">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="553ec-137">Per rinominare una colonna, sostituire la migrazione generata in precedenza con la seguente:</span><span class="sxs-lookup"><span data-stu-id="553ec-137">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="553ec-138">Durante il processo di scaffolding della migrazione viene visualizzato un avviso se un'operazione può causare una perdita di dati, ad esempio nel caso della rimozione di una colonna.</span><span class="sxs-lookup"><span data-stu-id="553ec-138">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="553ec-139">Se viene visualizzato questo avviso, assicurarsi in particolare di esaminare il codice delle migrazioni per verificarne l'accuratezza.</span><span class="sxs-lookup"><span data-stu-id="553ec-139">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="553ec-140">Aggiunta di SQL non elaborato</span><span class="sxs-lookup"><span data-stu-id="553ec-140">Adding raw SQL</span></span>

<span data-ttu-id="553ec-141">Sebbene la ridenominazione di una colonna possa essere eseguita tramite un'API incorporata, in molti casi ciò non è possibile.</span><span class="sxs-lookup"><span data-stu-id="553ec-141">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="553ec-142">È ad esempio possibile che si desideri sostituire le `FirstName` proprietà e esistenti `LastName` con una singola `FullName` Proprietà nuova.</span><span class="sxs-lookup"><span data-stu-id="553ec-142">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="553ec-143">La migrazione generata da EF Core sarà la seguente:</span><span class="sxs-lookup"><span data-stu-id="553ec-143">The migration generated by EF Core will be the following:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="553ec-144">Come in precedenza, questo potrebbe causare una perdita di dati indesiderata.</span><span class="sxs-lookup"><span data-stu-id="553ec-144">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="553ec-145">Per trasferire i dati dalle colonne precedenti, è necessario ridisporre le migrazioni e introdurre un'operazione SQL non elaborata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="553ec-145">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="553ec-146">Modifiche arbitrarie tramite SQL non elaborato</span><span class="sxs-lookup"><span data-stu-id="553ec-146">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="553ec-147">SQL non elaborato può essere utilizzato anche per gestire gli oggetti di database che EF Core non è in grado di riconoscere.</span><span class="sxs-lookup"><span data-stu-id="553ec-147">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="553ec-148">A tale scopo, aggiungere una migrazione senza apportare modifiche al modello; verrà generata una migrazione vuota, che potrà quindi essere popolata con operazioni SQL non elaborate.</span><span class="sxs-lookup"><span data-stu-id="553ec-148">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="553ec-149">Ad esempio, la seguente migrazione crea una SQL Server stored procedure:</span><span class="sxs-lookup"><span data-stu-id="553ec-149">For example, the following migration creates a SQL Server stored procedure:</span></span>

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> <span data-ttu-id="553ec-150">`EXEC` viene utilizzato quando un'istruzione deve essere la prima o una sola in un batch SQL.</span><span class="sxs-lookup"><span data-stu-id="553ec-150">`EXEC` is used when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="553ec-151">Può anche essere usato per aggirare gli errori del parser negli script di migrazione idempotente che possono verificarsi quando le colonne a cui si fa riferimento non esistono attualmente in una tabella.</span><span class="sxs-lookup"><span data-stu-id="553ec-151">It can also be used to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="553ec-152">Questa operazione può essere utilizzata per gestire qualsiasi aspetto del database, tra cui:</span><span class="sxs-lookup"><span data-stu-id="553ec-152">This can be used to manage any aspect of your database, including:</span></span>

<span data-ttu-id="553ec-153">_ Stored procedure</span><span class="sxs-lookup"><span data-stu-id="553ec-153">_ Stored procedures</span></span>
* <span data-ttu-id="553ec-154">Ricerca full-text</span><span class="sxs-lookup"><span data-stu-id="553ec-154">Full-Text Search</span></span>
* <span data-ttu-id="553ec-155">Funzioni</span><span class="sxs-lookup"><span data-stu-id="553ec-155">Functions</span></span>
* <span data-ttu-id="553ec-156">Trigger</span><span class="sxs-lookup"><span data-stu-id="553ec-156">Triggers</span></span>
* <span data-ttu-id="553ec-157">Viste</span><span class="sxs-lookup"><span data-stu-id="553ec-157">Views</span></span>

<span data-ttu-id="553ec-158">Nella maggior parte dei casi, EF Core effettuerà automaticamente il wrapping di ogni migrazione nella propria transazione durante l'applicazione delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="553ec-158">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="553ec-159">Sfortunatamente, alcune operazioni di migrazione non possono essere eseguite all'interno di una transazione in alcuni database. in questi casi, è possibile rifiutare esplicitamente la transazione passando `suppressTransaction: true` a `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="553ec-159">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="553ec-160">Se `DbContext` si trova in un assembly diverso da quello del progetto di avvio, è possibile specificare in modo esplicito i progetti di destinazione e di avvio negli [strumenti della console di Gestione pacchetti](xref:core/cli/powershell#target-and-startup-project) o negli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="553ec-160">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="553ec-161">Rimuovere una migrazione</span><span class="sxs-lookup"><span data-stu-id="553ec-161">Remove a migration</span></span>

<span data-ttu-id="553ec-162">Dopo l'aggiunta di una migrazione ci si rende talvolta conto che prima di applicarla sono necessarie altre modifiche al modello di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="553ec-162">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="553ec-163">Per rimuovere l'ultima migrazione, usare questo comando.</span><span class="sxs-lookup"><span data-stu-id="553ec-163">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="553ec-164">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="553ec-164">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="553ec-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="553ec-165">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="553ec-166">Dopo la rimozione della migrazione, è possibile apportare le modifiche aggiuntive al modello. La migrazione può quindi essere aggiunta di nuovo.</span><span class="sxs-lookup"><span data-stu-id="553ec-166">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="553ec-167">Prestare attenzione a non rimuovere le migrazioni già applicate ai database di produzione.</span><span class="sxs-lookup"><span data-stu-id="553ec-167">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="553ec-168">In caso contrario, non sarà possibile ripristinarlo e potrebbe interrompere le ipotesi effettuate dalle successive migrazioni.</span><span class="sxs-lookup"><span data-stu-id="553ec-168">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="553ec-169">Elenco delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="553ec-169">Listing migrations</span></span>

<span data-ttu-id="553ec-170">È possibile elencare tutte le migrazioni esistenti nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="553ec-170">You can list all existing migrations as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="553ec-171">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="553ec-171">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[<span data-ttu-id="553ec-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="553ec-172">Visual Studio</span></span>](#tab/vs)

> [!NOTE]
> <span data-ttu-id="553ec-173">Questo comando è stato aggiunto in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="553ec-173">This command was added in EF Core 5.0.</span></span>

```powershell
Get-Migration
```

<span data-ttu-id="553ec-174">\*\*_</span><span class="sxs-lookup"><span data-stu-id="553ec-174">\*\*_</span></span>

## <a name="resetting-all-migrations"></a><span data-ttu-id="553ec-175">Reimpostazione di tutte le migrazioni</span><span class="sxs-lookup"><span data-stu-id="553ec-175">Resetting all migrations</span></span>

<span data-ttu-id="553ec-176">In alcuni casi estremi, potrebbe essere necessario rimuovere tutte le migrazioni e ricominciare.</span><span class="sxs-lookup"><span data-stu-id="553ec-176">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="553ec-177">Questa operazione può essere eseguita facilmente eliminando la cartella _ *Migrations* \* ed eliminando il database. a questo punto è possibile creare una nuova migrazione iniziale, che conterrà l'intero schema corrente.</span><span class="sxs-lookup"><span data-stu-id="553ec-177">This can be easily done by deleting your _ *Migrations* \* folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="553ec-178">È anche possibile reimpostare tutte le migrazioni e crearne una singola senza perdere i dati.</span><span class="sxs-lookup"><span data-stu-id="553ec-178">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="553ec-179">Questa operazione viene a volte definita "squashing" e implica alcune operazioni manuali:</span><span class="sxs-lookup"><span data-stu-id="553ec-179">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="553ec-180">Elimina la cartella **migrazioni**</span><span class="sxs-lookup"><span data-stu-id="553ec-180">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="553ec-181">Creazione di una nuova migrazione e generazione di uno script SQL</span><span class="sxs-lookup"><span data-stu-id="553ec-181">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="553ec-182">Nel database eliminare tutte le righe dalla tabella di cronologia migrazioni</span><span class="sxs-lookup"><span data-stu-id="553ec-182">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="553ec-183">Inserire una singola riga nella cronologia delle migrazioni per registrare che la prima migrazione è già stata applicata, perché le tabelle sono già presenti.</span><span class="sxs-lookup"><span data-stu-id="553ec-183">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="553ec-184">L'istruzione INSERT SQL è l'ultima operazione nello script SQL generato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="553ec-184">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="553ec-185">Quando la cartella **Migrations** viene eliminata, eventuali [codici di migrazione personalizzati](#customize-migration-code) andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="553ec-185">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="553ec-186">Tutte le personalizzazioni devono essere applicate manualmente alla nuova migrazione iniziale per poter essere mantenute.</span><span class="sxs-lookup"><span data-stu-id="553ec-186">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
